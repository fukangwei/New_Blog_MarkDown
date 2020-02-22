---
title: 高精度模板bign类
categories: C++应用代码
date: 2018-12-15 23:35:27
---
&emsp;&emsp;很不错的高精度模板，但这个模板只能算加、减、乘、除等基本运算，但操作减法的时候只能大数减小数，所以最重要的操作是`+`、`+=`、`-`、`-=`、`*`、`*=`、`/`、`/=`、`==`、`>=`等一些基本的运算：<!--more-->

``` cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <string>
#include <algorithm>

using namespace std;

const int MAXN = 410;

struct bign {
    int len, s[MAXN];
    bign () {
        memset ( s, 0, sizeof ( s ) );
        len = 1;
    }

    bign ( int num ) {
        *this = num;
    }

    bign ( const char* num ) {
        *this = num;
    }

    bign operator = ( const int num ) {
        char s[MAXN];
        sprintf ( s, "%d", num );
        *this = s;
        return *this;
    }

    bign operator = ( const char* num ) {
        for ( int i = 0; num[i] == '0'; num++ ) ; /* 去掉前导“0” */
        len = strlen ( num );

        for ( int i = 0; i < len; i++ ) {
            s[i] = num[len - i - 1] - '0';
        }

        return *this;
    }

    bign operator + ( const bign& b ) const {
        bign c;
        c.len = 0;

        for ( int i = 0, g = 0; g || i < max ( len, b.len ); i++ ) {
            int x = g;
            if ( i < len ) {
                x += s[i];
            }

            if ( i < b.len ) {
                x += b.s[i];
            }

            c.s[c.len++] = x % 10;
            g = x / 10;
        }

        return c;
    }

    bign operator += ( const bign& b ) {
        *this = *this + b;
        return *this;
    }

    void clean() {
        while ( len > 1 && !s[len - 1] ) {
            len--;
        }
    }

    bign operator * ( const bign& b ) {
        bign c;
        c.len = len + b.len;

        for ( int i = 0; i < len; i++ ) {
            for ( int j = 0; j < b.len; j++ ) {
                c.s[i + j] += s[i] * b.s[j];
            }
        }

        for ( int i = 0; i < c.len; i++ ) {
            c.s[i + 1] += c.s[i] / 10;
            c.s[i] %= 10;
        }

        c.clean();
        return c;
    }

    bign operator *= ( const bign& b ) {
        *this = *this * b;
        return *this;
    }

    bign operator - ( const bign& b ) {
        bign c;
        c.len = 0;

        for ( int i = 0, g = 0; i < len; i++ ) {
            int x = s[i] - g;

            if ( i < b.len ) {
                x -= b.s[i];
            }

            if ( x >= 0 ) {
                g = 0;
            } else {
                g = 1;
                x += 10;
            }

            c.s[c.len++] = x;
        }

        c.clean();
        return c;
    }

    bign operator -= ( const bign& b ) {
        *this = *this - b;
        return *this;
    }

    bign operator / ( const bign& b ) {
        bign c, f = 0;
        for ( int i = len - 1; i >= 0; i-- ) {
            f = f * 10;
            f.s[0] = s[i];
            while ( f >= b ) {
                f -= b;
                c.s[i]++;
            }
        }

        c.len = len;
        c.clean();
        return c;
    }

    bign operator /= ( const bign& b ) {
        *this = *this / b;
        return *this;
    }

    bign operator % ( const bign& b ) {
        bign r = *this / b;
        r = *this - r * b;
        return r;
    }

    bign operator %= ( const bign& b ) {
        *this = *this % b;
        return *this;
    }

    bool operator < ( const bign& b ) {
        if ( len != b.len ) {
            return len < b.len;
        }

        for ( int i = len - 1; i >= 0; i-- ) {
            if ( s[i] != b.s[i] ) {
                return s[i] < b.s[i];
            }
        }

        return false;
    }

    bool operator > ( const bign& b ) {
        if ( len != b.len ) {
            return len > b.len;
        }

        for ( int i = len - 1; i >= 0; i-- ) {
            if ( s[i] != b.s[i] ) {
                return s[i] > b.s[i];
            }
        }

        return false;
    }

    bool operator == ( const bign& b ) {
        return ! ( *this > b ) && ! ( *this < b );
    }

    bool operator != ( const bign& b ) {
        return ! ( *this == b );
    }

    bool operator <= ( const bign& b ) {
        return *this < b || *this == b;
    }

    bool operator >= ( const bign& b ) {
        return *this > b || *this == b;
    }

    string str() const {
        string res = "";
        for ( int i = 0; i < len; i++ ) {
            res = char ( s[i] + '0' ) + res;
        }

        return res;
    }
};

istream& operator >> ( istream& in, bign& x ) {
    string s;
    in >> s;
    x = s.c_str();
    return in;
}

ostream& operator << ( ostream& out, const bign& x ) {
    out << x.str();
    return out;
}

int main() {
    bign a, b, c, d, e, f, g;

    while ( cin >> a >> b ) {
        a.clean(), b.clean();
        c = a + b;
        d = a - b;
        e = a * b;
        f = a / b;
        g = a % b;
        cout << "a + b" << " = " << c << endl;
        cout << "a - b" << " = " << d << endl;
        cout << "a * b" << " = " << e << endl;
        cout << "a / b" << " = " << f << endl;
        cout << "a % b" << " = " << g << endl;

        if ( a != b ) {
            printf ( "YES\n" );
        } else {
            printf ( "NO\n" );
        }
    }

    return 0;
}
```