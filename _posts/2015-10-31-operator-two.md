---
layout: post
title: C++ 运算符重载II
date: 2015-10-31 11:01
categories: C++
tags: [C++]
---
**本例在上一例的基础上增加重载-=和*=的成员函数.**
```cpp
#include<iostream>
using namespace std;
class Complex { //复数类
    private:    //定义私有成员变量
        double real;    //定义实数部
        double image;   //定义虚数部
    public:
        Complex(void):real(0),image(0) {}   //定义参数为空的构造函数
        Complex(double rp):real(rp),image(0) {} //定义只有实数部的构造函数
        Complex(double rp,double ip):real(rp),image(ip) {}  //定义参数同时制定实数和虚数的构造函数
        ~Complex() {}  //定义析构函数（无特定操作可不写）

        Complex operator+(const Complex &x) const;  //声明重载运算符+
        Complex operator-(const Complex &x) const;  //声明重载运算符-
        Complex operator*(const Complex &x) const;  //声明重载运算符*
        Complex operator/(const Complex &x) const;  //声明重载运算符/
        bool operator==(const Complex &x) const;    //声明重载运算符==
        Complex& operator+=(const Complex &x);      //声明重载运算符+=

        Complex& operator-=(const Complex &x);      //声明重载运算符-=
        Complex& operator*=(const Complex &x);      //声明重载运算符*=

        void Print(void) const; //定义类成员输出函数
};
inline Complex Complex::operator+(const Complex &x) const { //重载运算符的实际函数体
    return Complex(real + x.real, image + x.image);
}
inline Complex Complex::operator-(const Complex &x) const {
    return Complex(real - x.real, image - x.image);
}
inline Complex Complex::operator*(const Complex &x) const {
    return Complex(real * x.real - image * x.image, real * x.image + image * x.real);
}
Complex Complex::operator/(const Complex &x) const {
    double m;
    m = x.real * x.real + x.image * x.image;
    return Complex((real * x.real + image * x.image) / m, (image * x.real - real * x.image) / m);
}
inline bool Complex::operator==(const Complex &x) const {   //运算符==判断是否相等，应返回bool类型的值
    return bool(real == x.real && image == x.image);
}
Complex& Complex::operator+=(const Complex &x) {    //因为+=的结果是将被加数加在自己的成员函数上，所以返回自身的指针
    real += x.real;
    image += x.image;
    return *this;
}
Complex& Complex::operator-=(const Complex &x) {
    real -= x.real;
    image -= x.image;
    return *this;
}
Complex& Complex::operator*=(const Complex &x) {
    real = real * x.real - image * x.image;
    image = real * x.image + image * x.real;
    return *this;
}
void Complex::Print(void) const {   //输出函数
    cout<<"("<<real<<","<<image<<"i)"<<endl;
}

int main(void) {    //测试函数
    Complex a(3, 5), b(2, 3), c;
    c = a + b * a / b - b;
    c.Print();

    a += b;
    a.Print();

    a *= b; //测试*= 
    a.Print();

    a -= b; //测试-= 
    a.Print();

    if(a == c) cout<<"对象a等于对象c"<<endl;
    else cout<<"对象a不等于对象c"<<endl;

    return 0;
}
```