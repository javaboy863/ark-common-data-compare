# 1.什么是ark-common-data-compare？
&emsp;&emsp;ark-common-data-compare是ark系列框架中的负责数据对比的组件。
# 2.ark-common-data-compare解决了什么问题？
&emsp;&emsp;在互联网系统中，我们经常会在重构系统，重构模块或接入中台时，做一些数据的迁移工作，当数据迁移完毕后，需要对比迁移的结果，因此抽象出数据对比组件，以便在后续的开发中复用，避免重复开发。       
&emsp;&emsp;该数据对比工具，针对新老接口的返回结果进行比对，失败后打印具体的不一致字段，方便问题的排查。

# 3.功能特性
- 支持基于硬编码方式进行对比
- 支持基于注解方式进行对比

# 4.ark-common-data-compare组件接入
```
1、在pom文件中添加mavne依赖：
    <dependencies>
        <dependency>
            <groupId>com.ark.common</groupId>
            <artifactId>ark-common-data-compare</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>
2、采用springboot自动注入，无需进行bean配置    
```

# 5.ark-common-data-compare组件使用
```
1、使用方式1，基于硬编码
try {
    OrderDetailReq orderReq = new OrderDetailReq(admin.getid(), id, sn, admin.getId(), id);
    ThreadPoolExecutorUtil.execute(new CompareToolsThread(orderReq, order, OrderQueryServiceImpl.class, "getOrderDetail"));
} catch (Exception e) {
    log.error("xxxxxxxxx");
}

CompareToolsThread对应参数
(1)接口调用参数
(2)老接口最后组装的结果，目前只支持JSONObject格式的
(3)调用的新接口class
(4)调用方法名

适用场景：接口无返回值的情况，采用out.write这种方式返回前端的，或者返回JsonObject的



2、使用方式2，基于注解
适用场景：新老接口有返回值，返回结果是对象，@Compare注解，可以加在方法或者字段上

@Compare(invokeClass = OrderQueryServiceImpl.class, methodName = "getOrderDetailOfNoPay", paramClass = OrderDetailReq.class)
public Result<OrderRes> getOrderDetailOfPay(OrderDetailReq req) {
    //doSomething
}



3、使用方式3，传入2个对象
适用场景：A代表基准，B代表参照，比如新接口返回的是A，老接口返回的是B，那么用新接口的返回值和老街口对比，可以采用下面这种方式
CompareToolsOfBeanThread compareToolsOfBeanThread = new CompareToolsOfBeanThread();
compareToolsOfBeanThread.compareValue(A, B);

```

