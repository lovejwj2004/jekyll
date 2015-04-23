---
layout:     post
title:      "Exception in Java"
subtitle:   "Excpetion"
date:       2015-04-23 21:20:00
author:     "chne"
header-img: "img/Exception.jpg"
---

<p>Java中的异常从内容上来说，实在没什么太复杂的内容。不过就是，可能有异常的代码需要使用Try/Catch语句包括起来或者是向上抛出，finally中的语句不管怎样都会被执行。但是，还是那句老话说起来容易做起来难异常用的不好影响系统性能而且还找不到错误原因，所以Java中的异常是门学问。</p>

> 看起来简单的事情从不简单

<h2 class="section-heading">总览</h2>
<a href="#">
    <img src="{{ site.baseurl }}/img/exception-01.png" alt="Java Exception Overview">
</a>

<h3>Class</h3>

Java中的异常从类的继承关系上来说，都是继承自Throwable类。   
再往下细分分为:

+ Exception
+ Error

这两个类其中Exception中包含RuntimeException这个子类。

<h3>Checked VS Unchecked Exception</h3>

Java中的异常又按照是否需要通过代码去处理分为Checked和Unchecked Exception  

Checked Exception:   
  继承自Exception类，必须针对代码中可能抛出的异常进行处理可以通过try/catch进行处理或者是继续使用throw将异常往上抛出.   

Unchecked Exception:   
  继承自RuntimeException,对于代码无需进行特殊处理但是代码可能会抛出异常。

<h2 class="section-heading">How to use</h2>

Java中到底是使用Unchecked还是Checkd异常需要根据具体的情况来决定，如果我们对发生的异常能够通过操作恢复的话那么我们应该使用Checked异常，但是如果我们对于发生的异常无能为力或者说什么都做不鸟的话这个时候我们应该使用Unchecked异常。

<h3>Best Practices</h3>

+  
对于资源使用的情况，一定要注意释放。（PS:如果最后实在无法释放，我们这个时候抛出的是一个unchecked Exception因为这个时候我们无能为力）
<pre><code>
public void dataAccessCode(){
    Connection conn = null;
    try{
        conn = getConnection();
        ..some code that throws SQLException
    }catch(SQLException ex){
        ex.printStacktrace();
    } finally{
        DBUtil.closeConnection(conn);
    }
}

class DBUtil{
    public static void closeConnection
        (Connection conn){
        try{
            conn.close();
        } catch(SQLException ex){
            logger.error("Cannot close connection");
            throw new RuntimeException(ex);
        }
    }
}
</code></pre>
+ 
永远不要拿Exception来进行流程控制,因为Exception重构的堆栈路径是很耗资源.
<pre><code>
public void useExceptionsForFlowControl() {
    try {
        while (true) {
            increaseCount();
        }
    } catch (MaximumCountReachedException ex) {
    }
    //Continue execution
}

public void increaseCount()
    throws MaximumCountReachedException {
    if (count >= 5000)
        throw new MaximumCountReachedException();
}
</code></pre>
+ 
不要忽略异常,一般来说如果代码抛出Checked Exception那么代码是在尝试告诉你信息希望你能够恢复此段代码，如果不是这样的话那么果断应该换成Unchecked Exception。所以，对于Checked Exception的异常不要直接忽略掉.
+
同一个异常只记录一次，避免造成不必要的亦或。
+ 
不要将catch Exception这样的语句放在异常捕捉的最开始，因为所有的异常都是继承Exception那么其它的子类异常将直接被Exception的catch捕捉到。