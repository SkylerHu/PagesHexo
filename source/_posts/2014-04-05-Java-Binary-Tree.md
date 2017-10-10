---
title: Java先序序列构造二叉树
date: 2014-04-05 20:32
tags:
  - Java
  - DataStructure
---
#### BinaryTree.java

```Java
package com.anjoyo.data_structures.tree;  
  
import java.util.Scanner;  
  
/**  
 * 先序序列构造二叉树 
 * @author HLP 
 * 
 */  
public class BinaryTree {  
    static String[] arr = new String[100];  
    static int index = 0;  
      
    static {  
        @SuppressWarnings("resource")  
        Scanner scanner = new Scanner(System.in);  
        String str;  
        do {  
            str = scanner.next();  
            if ("over".equals(str)) {  
                break;  
            }  
            arr[index++] = str;  
        } while (true);  
        index = 0;  
    }  
      
    public static TNode create() {  
        TNode node = null;  
        String data = arr[index++];  
        if (! "null".equals(data)) {  
            node = new TNode(data);  
            node.lChild = create();  
            node.rChild = create();  
        }  
        return node;  
    }  
      
    public static void print(TNode node) {  
        if (node == null) {  
            return;  
        }else {  
            System.out.print(node.data + " --> ");  
            print(node.lChild);  
            print(node.rChild);  
        }  
    }  
      
    public static void main(String[] args) {  
        TNode biTree = null;  
        biTree = create();  
          
        print(biTree);  
    }  
}
```

#### TNode.java

```Java
package com.anjoyo.data_structures.tree;  
  
public class TNode {  
    String data;//值  
    TNode lChild;//左孩子  
    TNode rChild;//右孩子  
      
    public TNode(String data) {  
        this.data = data;  
        this.lChild = null;  
        this.rChild = null;  
    }  
}
```

#### 测试Test.java
```Java
package com.anjoyo.data_structures.tree;  
  
class T {  
    int data;  
      
    public T(){}  
    public T(int data) {  
        this.data = data;  
    }  
}  
  
public class Test {  
      
    static T createT(){  
        T t2 = new T(32);  
//      t.data = 34;  
//      t = t2;  
        return t2;  
    }  
      
    public static void main(String[] args) {  
        T t = new T();  
//      createT(t);  
        System.out.println(t);  
        System.out.println(t.data);  
          
        T t2 = null;  
        t2 = createT();  
        System.out.println(t2);  
        System.out.println(t2.data);  
    }  
}
```
