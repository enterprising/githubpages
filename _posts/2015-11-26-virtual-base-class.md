---
layout: post
title: C++ 虚基类的使用示例
date: 2015-11-26 15:36
categories: C++
tags: [C++]
---
```cpp
#include<iostream>
using namespace std;
class shape{
    public:
        virtual void SurfaceArea(){
        }
        virtual void Volume(){
        }
};
class Cube:public shape{
    double length;
    public:
        Cube(double length):length(length){

        }
        void SurfaceArea(){
            cout<<"表面积是："<<length*length*6<<endl;
        }
        void Volume(){
            cout<<"体积是："<<length*length*length<<endl;
        }
};
class Cylinder:public shape{
    double radius;
    double heigth;
    public:
        Cylinder(double radius,double heigth):radius(radius),heigth(heigth){
        }
        void SurfaceArea(){
            cout<<"表面积是："<<(radius*2*3.14)*heigth+radius*radius*3.14*2<<endl;
        }
        void Volume(){
            cout<<"体积是："<<radius*radius*3.14*heigth<<endl;
        }
};
class Ball:public shape{
    double radius;
    public:
        Ball(double radius):radius(radius){
        }
        void SurfaceArea(){
            cout<<"表面积是："<<4*3.14*radius*radius<<endl;
        }
        void Volume(){
            cout<<"体积是："<<4.0/3.0*3.14*radius*radius*radius<<endl;
        }
};
int main(void)
{
    shape *s;
    s = new Cube(5);
    s->SurfaceArea();
    s->Volume();
    s = new Cylinder(1,1);
    s->SurfaceArea();
    s->Volume();
    s = new Ball(2);
    s->SurfaceArea();
    s->Volume();
    return 0;
}
```