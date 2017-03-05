---
layout: post
title: C++ 成员函数与友元函数
date: 2015-10-31 11:25
categories: C++
tags: [C++]
---
**在[C++ 运算符重载]一文的基础上，增加两个判断复数是否相等的函数，一个为成员函数，一个为友元函数**
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

        bool Charge_member(Complex &x); //声明判断复数相等的成员函数
        friend bool Charge_friend(Complex &x, Complex &y);  //声明类外的一个判断相等的函数为友元函数（这个函数不是类的成员函数，是将类外的某个函数定义为自己的友元函数,只有定义为友元函数之后才能访问类的私有变量）

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

inline bool Complex::Charge_member(Complex &x) {    //直接判断
    return bool(real == x.real && image == x.image);
}

bool Charge_friend(Complex &x, Complex &y) {    //需声明两个复数类的参数
    return bool(y.real == x.real && y.image == x.image);
}

void Complex::Print(void) const {   //输出函数
    cout<<"("<<real<<","<<image<<"i)"<<endl;
}

int main(void) {    //测试函数
    Complex a(3, 5), b(2, 3), c;
    c = a + b * a / b - b;
    c.Print();

    if(a.Charge_member(b)) cout<<"这两个复数是相等的(成员函数)"<<endl;
    else  cout<<"这两个复数是不相等的(成员函数)"<<endl;

    if(Charge_friend(a,b))  cout<<"这两个复数是相等的(友元函数)"<<endl;
    else  cout<<"这两个复数是不相等的(友元函数)"<<endl;

    return 0;
}
```