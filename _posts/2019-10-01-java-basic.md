---
layout: post
title: Java基础
categories: Java
description: Java基础笔试题及面试题
keywords: Java
---

1. 找出所有三位数中的水仙花数

   ```
    public void getNarcissusNums(){
       int g = 0, s = 0, b = 0, sum = 0;
       for(int i = 100;i <= 999;i++){
           b = i / 100;    // 百位数
           s = (i - b * 100)/10;   // 十位数
           g = i - b * 100 - s * 10;   // 个位数
           sum = (int)(Math.pow(b, 3) + Math.pow(s, 3) + Math.pow(g, 3))
           if (sum == i){
               System.out.println(i);
           }
       }
    }
   ```

1. 输入2, 5, 计算 2 + 22 + 222 + 222 + 22222

   ```
    public int caculate(int a, int b){
       int sum = 0, curr = a;
       if (b == 1){
            sum = a;
       }
       if (b > 1){
            sum += a;
            for(int i = 1; i < b; i++){
                curr = (int)(a * Math.pow(10, i) + curr);
                sum += curr;
            }
       }
       return sum;
    }
   ```

1. 从数组中找出重复元素及其所在位置
   ```
    public static void main(String[] args){
        Integer[] numbers={12, 18, 19, 15, 26, 29, 49, 15, 12, 19, 29, 12, 18};
        // 集合用来存放不重复的数字
        ArrayList<Integer> numberArray = new ArrayList<>();
        // 嵌套集合用来存放重复数字的角标
        ArrayList<ArrayList<Integer>> numberIndex = new ArrayList<>();
        // 集合中存放重复出现的数字
        ArrayList<Integer> sameArray = new ArrayList<>();
        for(int i = 0; i < numbers.length; i++){
            // 只遍历一遍
            if(!numberArray.contains(numbers[i])){
                // 新数字存进集合
                numberArray.add(numbers[i]);
            }
        }else{
            if(!sameArray.contains(numbers[i])){
                // 重复数字存进集合
                sameArray.add(numbers[i]);
                // 在这个嵌套集合中创建存有这个数字角标的集合
                ArrayList<Integer> numberHead = new ArrayList<>();
                // 把第一次出现该数字的角标存进集合
                numberHead.add(new Integer(numberArray.indexOf(numbers[i])));
                numberIndex.add(numberHead);
            }
            // 把这次出现的该数字的角标存进集合
            numberIndex.get(sameArray.indexOf(numbers[i])).add(new Integer(i));
        }
    }
    // 输出结果
    for(int i = 0; i < sameArray.size();i++){
        System.out.println(sameArray.get(i) + "\t" + numberIndex.get(i).toString());
    }
   ```

1. 打印棱形

   ```
    public class PrintRhombus {
        public static void print(int size){
            if (size % 2 == 0) {
                size++; // 计算菱形大小
            }
            for (int i = 0; i < size / 2 + 1; i++){
                for (int j = size / 2 + 1; j > i + 1; j--){
                    System.out.print(" ");  // 输出左上角位置的空白
                }
                for (int j = 0; j < 2 * i + 1; j++) {
                    System.out.print("*");  // 输出菱形上半部边缘
                }
                System.out.println();   // 换行
            }
        }
    }
    
   ```

1. SQL题

CARD借书卡：    CNO卡号,  NAME姓名, CLASS班级
BOOKS图书：     BNO书号,  BNAME书名, AUTHOR作者, PRICE单价, QUANTITY库存册数
BORROW借书记录：CNO借书卡号, BNO书号, RDATE还书日期

1. 找出借书超过5本的读者，输出借书卡号及所借图书册数

   ```mysql
    select cno, count(*) from borrow group by con having count(*) > 5;
   ```

2. 查询当前接了“计算方法”但没有借“计算方法习题集”的读者，输出其借书卡号，并按卡号降序排序输出

   ```mysql
    select a.cno from borrow a, books b where a.bno = b.bno and b.bname = '计算方法'
        and not exists
        (select * from borrow aa, books bb where aa.bno = bb.bno and bb.name = '计算方法习题集' and aa.cno = a.cno)
        order by a.cno desc
   ```

3. 将“c01”班同学所借图书的还期都延长一周
   ```mysql
    update b set rdate = date_add(day, 7, b.rdate)
       from card a, borrow b where a.cno = b.cno and a.class = 'c01'
   ```

4. 从books表中删除当前无人借阅的图书记录
   ```mysql
    delete a from books a where not exists(select * from borrow where bno = a.bno)
   ```

5. 如果经常按书名查询图书信息，请建立合适的索引

   ```mysql
    create index idex_books_bname on books(bname)
   ```
