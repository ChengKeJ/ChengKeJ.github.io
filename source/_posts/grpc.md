---
layout: post
title:  "什么是gRPC？有什么优点？"
categories: 设计
tags:  设计
date: 2023/04/01 14:46:25
---

RPC（远程过程调用）被称为“远程”，是因为它可以在微服务架构下将服务部署在不同的服务器上时，在远程服务之间进行通信。从用户的角度来看，它就像是一个本地函数调用。

下面的图表说明了gRPC的整体数据流程。

![b98afdcd-b567-4c90-9f47-5358df0adda6_1280x1619.jpeg](https://res.craft.do/user/full/8cf3a6c7-2a00-8ee8-0195-0eb38fda2eeb/5610CB5D-8792-4108-B0B9-D1582C6C9B59_2/bOxnEzyEycV4MsFrPOFkkGcHzgT4O1YNv1fHHOJTy6kz/b98afdcd-b567-4c90-9f47-5358df0adda6_1280x1619.jpeg)

<!--more-->

第1步：客户端发出REST调用。请求正文通常以JSON格式呈现。

第2-4步：订单服务（gRPC客户端）接收REST调用，对其进行转换，并向付款服务发出RPC调用。gPRC将客户端存根编码为二进制格式并将其发送到底层传输层。

第5步：gRPC通过HTTP2将数据包通过网络发送。由于二进制编码和网络优化，gRPC的速度比JSON快5倍。

第6-8步：付款服务（gRPC服务器）接收来自网络的数据包，对其进行解码并调用服务器应用程序。

第9-11步：结果从服务器应用程序返回，并被编码并发送到传输层。

第12-14步：订单服务接收数据包，对其进行解码，并将结果发送到客户端应用程序。

在使用gRPC时，需要定义protobuf消息和服务定义。protobuf消息是通信中传输的数据类型，而服务定义则描述了如何使用这些消息来实现RPC方法。

### 代码示例

下面是一个使用gRPC实现简单加法功能的代码示例：

```other
syntax = "proto3";
package calculator;

service Calculator {
  rpc Add (AddRequest) returns (AddResponse) {}
}

message AddRequest {
  int32 a = 1;
  int32 b = 2;
}

message AddResponse {
  int32 result = 1;
}
```

上面的代码定义了一个名为`Calculator`的服务，该服务具有一个名为`Add`的RPC方法。此方法将一个请求消息`AddRequest`作为输入，并返回一个响应消息`AddResponse`。

`AddRequest`和`AddResponse`是protobuf消息。它们都具有简单的整数字段。`AddRequest`包含两个整数字段a和b，表示要相加的两个数字。`AddResponse`包含一个整数字段result，表示加法的结果。

现在，我们需要在客户端和服务器上实现这个服务。在开始实现客户端代码之前，需要先引入gRPC依赖库。可以通过在Maven或Gradle中添加以下依赖项来实现：

```other
// Maven
<dependency>
  <groupId>io.grpc</groupId>
  <artifactId>grpc-netty-shaded</artifactId>
  <version>1.42.0</version>
</dependency>

// Gradle
implementation 'io.grpc:grpc-netty-shaded:1.42.0'
```

接下来，我们可以编写一个简单的Java客户端程序来调用我们之前定义的加法服务

```other
import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import io.grpc.StatusRuntimeException;
import com.example.grpc.AddRequest;
import com.example.grpc.AddResponse;
import com.example.grpc.MathServiceGrpc;

public class MathClient {
    private final ManagedChannel channel;
    private final MathServiceGrpc.MathServiceBlockingStub blockingStub;

    public MathClient(String host, int port) {
        channel = ManagedChannelBuilder.forAddress(host, port)
                .usePlaintext()
                .build();
        blockingStub = MathServiceGrpc.newBlockingStub(channel);
    }

    public void shutdown() throws InterruptedException {
        channel.shutdown().awaitTermination(5, TimeUnit.SECONDS);
    }

    public void add(int a, int b) {
        AddRequest request = AddRequest.newBuilder()
                .setA(a)
                .setB(b)
                .build();
        AddResponse response;
        try {
            response = blockingStub.add(request);
        } catch (StatusRuntimeException e) {
            System.err.println("RPC failed: " + e.getStatus());
            return;
        }
        System.out.println("Result: " + response.getResult());
    }

    public static void main(String[] args) throws Exception {
        MathClient client = new MathClient("localhost", 50051);
        try {
            client.add(1, 2);
        } finally {
            client.shutdown();
        }
    }
}
```

在此代码中，我们首先创建了一个`ManagedChannel`实例，该实例连接到我们的服务器。然后，我们使用这个`ManagedChannel`实例创建一个新的`MathServiceGrpc.MathServiceBlockingStub`实例，该实例是通过gRPC自动生成的，可以用来调用我们定义的加法方法。

在`add`方法中，我们首先构建一个`AddRequest`实例，该实例包含了我们需要相加的两个整数。然后，我们使用我们之前创建的`MathServiceGrpc.MathServiceBlockingStub`实例调用`add`方法，并传递这个`AddRequest`实例作为参数。最后，我们将结果打印出来。

在`main`方法中，我们创建一个新的`MathClient`实例，并使用它来调用`add`方法。最后，我们在`finally`块中关闭了`MathClient`实例。

这个Java客户端代码可以通过以下命令来编译：

```other
$ javac MathClient.java
```

然后，我们可以使用以下命令来运行它：

```other
$ java MathClient
```

运行这个程序后，将输出以下内容：

```other
Result: 3
```

这表明我们的加法服务已经成功地在客户端和服务器之间通信了。

### 总结

- gRPC可以实现简单的RPC方法，在客户端和服务器之间成功地进行通信。
- protobuf消息和服务定义使得定义和使用RPC方法更容易。
- gRPC提供高效的网络传输和编码，从而提高了通信速度和可靠性。
- gRPC为开发人员提供了轻松构建分布式系统的强大工具。
- 在微服务架构下，gRPC使服务之间的通信更加容易和高效。
- 在实际开发中需要注意潜在的问题，例如网络延迟和数据大小限制，需要仔细设计服务接口和消息类型，以确保通信的稳定性和可靠性。
- gRPC是值得尝试的分布式系统开发工具，提供高效的RPC通信和易于使用的接口定义。

