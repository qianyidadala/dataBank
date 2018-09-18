# java总结

1. ### Java基础

   1. #### 八大基本类型以及包装类基本类型

      | 基本类型    | 大小(字节) | 默认值          | 封装类       |
      | ------- | ------ | ------------ | --------- |
      | byte    | 1      | (byte)0      | Byte      |
      | short   | 2      | (short)0     | Short     |
      | int     | 4      | 0            | Integer   |
      | long    | 8      | 0L           | Long      |
      | float   | 4      | 0.0f         | Float     |
      | double  | 8      | 0.0d         | Double    |
      | boolean | -      | false        | Boolean   |
      | char    | 2      | \u0000(null) | Character |

   2. #### 数组

      ```java
      //必须在后面设置数组大小
      //一维数组
      int[] array = new int[10];
      int[] array1 = new int{1,2,3}; 
      //二维数组
      int[][] array = new int[10][10];
      int[][] array = new int{{1,2,3},{4,5,6}};
      ```

   3. #### 排序

      #### ![0c22a7227ec60c498db581481b073511](/img/0c22a7227ec60c498db581481b073511.png)

      1. **直接插入排序**

         **基本思想** ：在要排序的一组数中，假设前面(n-1)[n>=2]个数已经是拍好顺序的，现在要把第n个数查到前面的有序数中，使得这n个数也是排好顺序的。如此反复循环，直到全部排好顺序。

         **简单的来说**：先排好一个数，在把后面的数拿到前面来比对，进行排序，直到这个数组所有数据排列完成时结束。

         ```java
         public static void main(String[] args) {
         	int a[] = { 49, 38, 65, 97, 76, 13, 27, 49, 78, 34, 12, 64, 5, 4, 62,99, 98, 54, 56, 17, 18, 23, 34, 15, 35, 25, 53, 51 };
         	int temp = 0;
         	for (int i = 1; i < a.length; i++) {
         		int j = i - 1;
         		temp = a[i];
         		for (; j >= 0 && temp < a[j]; j--) {
         			a[j + 1] = a[j]; // 将大于temp的值整体后移一个单位
         		}
         		a[j + 1] = temp;
         	}
         	for (int i = 0; i < a.length; i++){
         		System.out.println(a[i]);
         	}
         }

         ```

         ​

      2. **希尔排序**

         **基本思想** ：算法先要将要排序的一组数按摸个增量d(n/2,n为要排序数的个数)分成若干组，每组记录的下标相差d。对每组中全部元素进行直接插入排序，然后再用一个较小的增量（d/2）对它进行分组，在每组中再进行直接插入排序。当增量减到1时，进行直接插入排序后，排序完成。

         **简单的来说** ：还没研究清楚emmmmm。

         ```java
         public static void main(String[] args) {
         	int a[] = { 1, 54, 6, 3, 78, 34, 12, 45, 56, 100 };
         	double d1 = a.length;
         	int temp = 0;
         	while (true) {
         		d1 = Math.ceil(d1 / 2);
         		int d = (int) d1;
         		for (int x = 0; x < d; x++) {
         			for (int i = x + d; i < a.length; i += d) {
         				int j = i - d;
         				temp = a[i];
         				for (; j >= 0 && temp < a[j]; j -= d) {
         					a[j + d] = a[j];
         				}
         				a[j + d] = temp;
         			}
         		}
         		if (d == 1){
         			break;
         		}
         	}
         	for (int i = 0; i < a.length; i++){
         		System.out.println(a[i]);
         	}
         }

         ```

      3. **简单选择排序**

         **基本思想** ：在要排序的一组数中，选出最小的一个数与第一个位置的数交换；然后在盛夏的数当中再找最小的与第二个位置的数交换，如此循环到倒数第二个数和最后一个数比较为止。

         **简单的说** ：先找到最小的放在第一个，再找到第二小的放在第二个位置，直到排序完成。

         ```java
         public static void main(String[] args) {
         	int a[] = { 1, 54, 6, 3, 78, 34, 12, 45 };
         	int position = 0;
         	for (int i = 0; i < a.length; i++) {
         		int j = i + 1;
         		position = i;
         		int temp = a[i];
         		for (; j < a.length; j++) {
         			if (a[j] < temp) {
         				temp = a[j];
         				position = j;
         			}
         		}
         		a[position] = a[i];
         		a[i] = temp;
         	}
         	for (int i = 0; i < a.length; i++){
         		System.out.println(a[i]);
         	}
         }

         ```

      4. **冒泡排序**

         **基本思想** ：在要排序的一组数中，对当前还未排好序的范围内的全部数，自上而下对相邻的两个数依次进行比较和调整，让较大的数往下沉，较小的往上冒。即：每当两相邻的数比较后发现它们的排序与排序要求相反时，就将它们互换。

         **简要** ：每个数都进行一次比较。

         ```java
         public static void main (String[] args){
           	int[] a = {49, 38, 65, 97, 76, 13, 27, 49, 78, 34, 12, 64, 5, 4, 62, 99, 98, 54, 56, 17, 18, 23, 34, 15, 35, 25, 53, 51};
           	int num = 0;
           for(int i = 0; i <a.lenth - 1 ;i++){
             for(int j = 0; j <a.lenth - 1 - i; j++){
               if(a[j] >a[j+1]){
                	num = a[j];
                 a[j] = a[j+1];
                 a[j+1] = num;
               }
             }
           }
         }
         ```

         ​

      5. **快速排序**

      6. **堆排序**

      7. **归并排序**

      8. **基数排序**

   4. #### 数据结构

   5. #### 面向对象

   6. #### 垃圾回收

   7. #### 集合

   8. #### util包

   9. #### 内部类

   10. #### java注解

   11. #### 类加载和反射

   12. #### xml解析

   13. #### 国际化

2. ### jsq+servlet

3. ### javaEE

### 

### 

