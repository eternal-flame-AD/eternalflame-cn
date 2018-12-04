---
title: 【AP CSA学习心得】（1）String的比较
author: Eternal-flame-AD
layout: post
identifier: 245fa87366d7ab34985b40d495bdb9c1
date: 2018-03-26T15:47:40+00:00
categories:
  - Java
  - 学习
tags:
  - AP
  - Computer Science A
  - Java

---
今天学习AP CSA时出现了一个问题：

{{< highlight java >}}
package loops;
import java.util.Scanner;
public class WhileLoop {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        String password="";
        while (password!="PASSWORD") {  //WARNING: password!="PASSWORD" is NOT going to work! This is causing an infinite loop
            System.out.print("Password please:");
            password=input.next();
            //password="PASSWORD"; //Uncomment this and the while loop will end
            System.out.println(password.length()); //8
            System.out.println("PASSWORD".length()); //8
            System.out.println(password=="PASSWORD"); //always false
        }
        System.out.println("OK.");
    }
}
{{< / highlight >}}

如上的代码，不论输入的字符串是否正确，循环均会一直进行，找了很久没有找到原因，后来发现是input.next()出来的String对象和直接&#8221;PASSWORD&#8221;出来的对象不等，应当使用String.equals()方法

然而，int等其它类型却没有这个问题

{{< highlight java >}}
package loops;
import java.util.Scanner;
public class WhileLoop {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        String password="";
        while (!password.equals("PASSWORD")) {  //WARNING: password!="PASSWORD" is NOT going to work!
            System.out.print("Password please:");
            password=input.next();
        }
        System.out.println("OK. Next challenge:");
        int intpassword=0;
        while (intpassword!=123456) {  //This one is OK
            System.out.print("Password please:");
            intpassword=input.nextInt();
        }
        System.out.print("OK");
    }
}
{{< / highlight >}}