---
layout: post
title: C++ 继承与多重继承
date: 2015-11-12 16:56
categories: C++
tags: [C++]
---
1. 编写一个学生类Student。要求：
    - 学生的保护属性有：学号（Num），姓名（Name），性别（Sex）.学生的公用成员函数有：ReadData( )用于输入Num、Name、Sex。OutputData( )用于输出Num、Name、Sex。
    - 在学生类的基础上派生出研究生类Graduate，该类新增私有属性研究方向（ResearchD），新增公用成员函数ResearchWork( )，该函数功能为输出学号和研究方向。
    - 编写main函数测试基类和派生类的各属性很函数的访问。修改派生类的派生方式以及基类成员的访问方式，再测试看看基类和派生类的属性访问方式有何不同。 
2. 在上述工程中增加一个Teacher（教师）类，采用多重继承方式由Student和Teacher类公有派生出新类Student_Teacher。要求：
    - Teacher类的保护成员有：Name、Sex、Title（职称）、Wages（工资）。两个基类中的Name、Sex同名，在引用这些数据成员时，指定作用域。
    - 在派生类Student_Teacher中，增加两个成员函数：ReadF1（）用于读入：Num、Name、Sex、Title、Wages。Show（）用于输出数据，在Show（）中，首先调用Student类的display（）函数用于输出Num、Name、Sex，然后再用cout语句输出Title、Wages。
    - 在类体中声明成员函数，在类外定义成员函数。
```cpp
#include<iostream>
using namespace std;
class People {
    protected:
        int Num;
        string Name;
        string Sex;
    public:
        void ReadData() {
        }
        void OutputData() {
        }
};
class Student:virtual public People {
    public:
        void ReadData(int Num,string Name,string Sex) {
            this->Num = Num;
            this->Name = Name;
            this->Sex = Sex;
        }
        void OutputData() {
            cout<<"学号:"<<Num<<endl;
            cout<<"姓名:"<<Name<<endl;
            cout<<"性别:"<<Sex<<endl;
        }
};
class Teacher:virtual public People {
    protected:
        string subject;
    public:
        void ReadData(int Num,string Name,string Sex,string subject) {
            this->Num = Num;
            this->Name = Name;
            this->Sex = Sex;
            this->subject = subject;
        }
        void OutputData() {
            cout<<"学号:"<<Num<<endl;
            cout<<"姓名:"<<Name<<endl;
            cout<<"性别:"<<Sex<<endl;
            cout<<"科目:"<<subject<<endl;
        }
};
class Graduate:public Student {
    private:
        string ResearchID = "计算机";
    public:
        void ResearchWork() {
            cout<<"研究方向是:"<<ResearchID<<endl;
        }
};
class Student_Teacher:public Student,public Teacher {
    protected:
        string Title;
        double Wages;
    public:
        void ReadData(int Num,string Name,string Sex,string subject,string Title,double Wages) {
            this->Num = Num;
            this->Name = Name;
            this->Sex = Sex;
            this->subject = subject;
            this->Title = Title;
            this->Wages = Wages;
        }
        void OutputData() {
            cout<<"学号:"<<Num<<endl;
            cout<<"姓名:"<<Name<<endl;
            cout<<"性别:"<<Sex<<endl;
            cout<<"科目:"<<subject<<endl;
            cout<<"职称:"<<Title<<endl;
            cout<<"工资:"<<Wages<<endl;
        }
};
int main() {
    Student s;
    s.ReadData(1,"张三","男");
    s.OutputData();
    Graduate g;
    g.ReadData(2,"李四","女");
    g.OutputData();
    g.ResearchWork();
    Teacher t;
    t.ReadData(3,"王五","男","数学");
    t.OutputData();
    Student_Teacher st;
    st.ReadData(4,"马六","女","语文","教授",5000);
    st.OutputData();
}
```

运行结果:  
![](http://olwt21mf4.bkt.clouddn.com/17-3-3/19631243-file_1488508935423_676e.png)