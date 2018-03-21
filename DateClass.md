/***********************************************
********************DateClass*******************
***********************************************/

```cpp
/***********************************************
******以下为头文件#include"date.h"(声明)********
***********************************************/

#pragma once

#include<iostream>
#include<assert.h>
#include<stdlib.h>

using namespace std;

class Date {
public:
    Date(size_t year = 1900, size_t month = 1, size_t day = 1);
    Date(const Date& date);
    bool operator==(const Date& d);
    bool operator!=(const Date& d);

    Date& operator=(const Date& d);
    bool operator>(const Date& d);
    bool operator>=(const Date& d);
    bool operator<=(const Date& d);
    bool operator<(const Date& d);

    Date& operator++();    //前置++
    Date operator++(int);    //后置++

    Date& operator--();    //前置--
    Date operator--(int);    //后置--

    Date operator+(int day);
    Date& operator+=(int day);

    Date operator-(int day);
    Date& operator-=(int day);
    int operator-(const Date& d);

    bool IsValid();
    size_t GetMonthDay(size_t year, size_t month);
    bool IsLeapYear(size_t year);

private:
    int _year;
    int _month;
    int _day;
};

/***********************************************
*******以下为date类各成员函数的实现代码*********
***********************************************/

#define _CRT_SECURE_NO_WARNINGS
#include"date.h"

int days[13] = { -1,31,28,31,30,31,30,31,31,30,31,30,31 };


Date::Date(size_t year, size_t month, size_t day)
:_year(year),
_month(month), 
_day(day)
{
    if (!(this->IsValid()))
    {
        assert(NULL);
    }
}

Date::Date(const Date& date)
    : _year(date._year),
    _month(date._month),
    _day(date._day) 
{}

//返回值为引用，优点:1.可实现连续赋值；2.用传值方式返回，则需要调用一次拷贝构造函数创建临时变量
Date& Date::operator=(const Date& d)                     
{
    _year = d._year;
    _month = d._month;
    _day = d._day;

    return *this;
}

bool Date::IsValid()
{
    return _month>0&&_month < 13 && _day>0
        && _day <= (this->GetMonthDay(_year, _month));
}

size_t Date::GetMonthDay(size_t year, size_t month)
{
    if (this->IsLeapYear(year))
    {
        days[2] = 29;
    }
    return days[month];
}

bool Date::IsLeapYear(size_t year)
{
    return year % 400 == 0 || (year % 4 == 0 && year % 100 != 0);
}

bool Date::operator==(const Date& d)
{
    return _year == d._year
        &&_month == d._month
        &&_day == d._day;
}
bool Date::operator!=(const Date& d)
{
    return !(*this == d);
}

bool Date::operator>(const Date& d)
{
    if (_year > d._year
        || _year == d._year&&_month > d._month
        || _year == d._year&&_month == d._month&&_day > d._day)
    {
        return true;
    }
    return false;
}
bool Date::operator>=(const Date& d)
{
    return *this > d || *this == d;
}
bool Date::operator<=(const Date& d)
{
    return !(*this > d);
}
bool Date::operator<(const Date& d)
{
    return !(*this >= d);
}

Date Date::operator+(int day)
{
    Date tmp(*this);
    tmp._day += day;
    while (tmp._day >tmp.GetMonthDay(tmp._year, tmp._month))
    {
        tmp._day -= tmp.GetMonthDay(tmp._year, tmp._month);
        tmp._month++;
        if (tmp._month > 12)
        {
            tmp._year++;
            tmp._month = 1;
        }
    }
    return tmp;
}

Date& Date::operator+=(int day)
{
    *this = *this + day;
    return *this;
}

Date& Date::operator++()         //前置++
{
    *this += 1;
    return *this;
}

Date Date::operator++(int)       //后置++
{
    Date tmp(*this);
    *this += 1;
    return tmp;
}

Date& Date::operator--()         //前置--
{
    *this -= 1;
    return *this;
}
Date Date::operator--(int)       //后置--
{
    Date tmp(*this);
    *this -= 1;
    return tmp;
}

Date Date::operator-(int day)
{
    Date tmp(*this);
    tmp._day -= day;
    while (tmp._day <= 0)
    {
        tmp._month--;
        tmp._day += tmp.GetMonthDay(tmp._year, tmp._month);
        if (tmp._month == 0)
        {
            tmp._year--;
            tmp._month = 12;
        }
    }
    return tmp;
}
Date& Date::operator-=(int day)
{
    *this = *this - day;
    return *this;
}

int Date::operator-(const Date& d)
{
    int day = 0;
    Date max_date(*this);
    Date min_date(d);
    if (*this < d)
    {
        max_date = d;
        min_date = *this;
    }
    while (min_date != max_date)
    {
        day++;
        min_date += 1;
    }
    if (*this < d)
    {
        return -day;
    }
    return day;
}

/***********************************************
************以下为测试代码**********************
***********************************************/

#define _CRT_SECURE_NO_WARNINGS
#include"date.h"

#define TEST_HEADER printf("============================================%s============================================\n",__FUNCTION__)

void TestLogicalOperator()
{
    TEST_HEADER;
    Date d1(2017, 8, 3);
    Date d2(d1);
    cout << (d1 == d2) << endl;

    Date d3(2018, 8, 3);
    cout << (d3 == d2) << endl;
    cout << (d1 != d3) << endl;

    cout << (d1 > d3) << endl;
    cout << (d1 <d3) << endl;
    cout << (d1 <= d2) << endl;
}

void TestArithmeticOperator()
{
    TEST_HEADER;
    Date d1(2017, 8, 3);
    Date d2(d1+10);
    Date d3(2017, 8, 31);
    cout << (d1 - d3) << endl;
    d1 += 40;
    d3--;
    --d3;
    d3 -= 30;

    //cout << (d1 + 100) << endl;
}


int main()
{
    TestLogicalOperator();
    TestArithmeticOperator();

    system("pause");
    return 0;
}

```