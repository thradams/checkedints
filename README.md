# checked ints


Checked integer arithmetic using KISS principle.
We basically need 3 operations (add, sub, mult).
The logic of this code is the same for char, short, int or long long.
The pratical usage is int, size_t and long long, unsigned long long in my opnion.
The unit test can run for all chars .. but not for ints. So we assume it works
using the same principles.

```c




#include <stdlib.h>
#include <assert.h>
#include <errno.h>
#include <stdio.h>
#include <limits.h>
#include <string.h>
#include <stdbool.h>

/*
* Code is the same for any signed and unsigned
*/
#define UNSIGNED_MAX ((unsigned short)UCHAR_MAX)
#define UNSIGNED_MIN ((unsigned short)0)

#define SIGNED_MAX ((signed short)SCHAR_MAX)
#define SIGNED_MIN ((signed short)SCHAR_MIN)

typedef unsigned char UNSIGNED_TYPE;
typedef signed char SIGNED_TYPE;

bool unsigned_sub(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b) {

    if (a < b)
        return false;

    *result = a - b;
    return true;
}

bool unsigned_mult(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b) {

    if (b == 0) {
        /*
          b cannot be zero in the next test
          so we solve this case here
        */
        *result = 0;
        return true;
    }

    if (a > UNSIGNED_MAX / b)
        return false;

    *result = a * b;
    return true;
}

bool unsigned_sum(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b)
{
    if (a > UNSIGNED_MAX - b) {
        //a=2
        //b=254
        return false;
    }
    *result = a + b;
    return true;
}

bool signed_sub(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) 
{
    if (a >= 0 && b >= 0) {
    }
    else if (a < 0 && b < 0) {
    }
    else {
        if (a < 0)
        {
            if (a < SIGNED_MIN + b)
            {
                // (-128) - (-1)
                return false;
            }
        }
        else
        {
            if (b == SIGNED_MIN)
            {
                // 0 - (-128)                
                return false;
            }

            if (a > SIGNED_MAX - (-b)) {
                /*
                *  1 - (-127)
                *  2 - (-126)                
                */
                return false;
            }
        }
    }

    *result = a - b;
    return true;
}

bool signed_sum(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) {

    if (a >= 0 && b >= 0) {
        /*both positive*/
        if (a > SIGNED_MAX - b)
        {
            //2+126
            return false;
        }
    }
    else if (a < 0 && b < 0) {

        if (a == SIGNED_MIN || b == SIGNED_MIN)
        {
            //(-128) + (-128)
            return false;
        }

        if (a < SIGNED_MIN - b) {
            // (-127) + (-127)
            return false;
        }
    }
    else {
        /*one positive another negative*/

    }

    *result = a + b;
    return true;
}


bool signed_mult(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) {

    if (a > 0 && b > 0) {
        if (a > SIGNED_MAX / b)
        {
            //2*64
            return false;
        }
    }
    else if (a < 0 && b < 0) {

        if (a == SIGNED_MIN || b == SIGNED_MIN) {
            //(-128)*(-128)
            return false;
        }

        if (-a > SIGNED_MAX / -b)
        {
            //-127 * -127
            return false;
        }
    }
    else {
        if (a == 0 || b == 0) {
            *result = 0;
            return true;
        }
        if (b > 0) {
            if (a < SIGNED_MIN / b)
                //(-127) * (2)
                return false;
        }
        else {
            if (b < SIGNED_MIN / a) {
                //2*(-128)
                return false;
            }
        }
    }

    *result = a * b;
    return true;
}


void signed_test()
{
    printf("signed\n");
    for (SIGNED_TYPE a = SIGNED_MIN; a != SIGNED_MAX; a++)
    {
        for (SIGNED_TYPE b = SIGNED_MIN; b != SIGNED_MAX; b++)
        {
            long long sub_result = ((long long)a) - ((long long)b);
            bool sub_result_ok = (sub_result >= SIGNED_MIN && sub_result <= SIGNED_MAX);

            long long add_result = ((long long)a) + ((long long)b);
            bool add_result_ok = (add_result >= SIGNED_MIN && add_result <= SIGNED_MAX);

            long long mult_result = ((long long)a) * ((long long)b);
            bool mult_result_ok = (mult_result >= SIGNED_MIN && mult_result <= SIGNED_MAX);


            SIGNED_TYPE sub_result2;
            bool sub_result_ok2 = signed_sub(&sub_result2, a, b);

            SIGNED_TYPE add_result2;
            bool add_result_ok2 = signed_sum(&add_result2, a, b);

            SIGNED_TYPE mult_result2;
            bool mult_result_ok2 = signed_mult(&mult_result2, a, b);


            if (sub_result_ok != sub_result_ok2)
            {
                printf("unsigned_sub(%d , %d)\n'", (int)a, (int)b);
                sub_result_ok2 = signed_sub(&sub_result2, a, b);

            }
            if (
                (sub_result_ok && sub_result_ok2) &&
                (sub_result != sub_result2)
                )
            {
                printf("unsigned_sub(%d , %d)\n'", (int)a, (int)b);
                sub_result_ok2 = signed_sub(&add_result2, a, b);
            }

            if (add_result_ok != add_result_ok2)
            {
                printf("add(%d , %d)\n'", (int)a, (int)b);
                add_result_ok2 = signed_sum(&add_result2, a, b);

            }
            if (
                (add_result_ok && add_result_ok2) &&
                (add_result != add_result2)
                )
            {
                printf("add(%d , %d)\n'", (int)a, (int)b);
                add_result_ok2 = signed_sum(&add_result2, a, b);
            }


            if (mult_result_ok != mult_result_ok2)
            {
                printf("mult(%d , %d)\n'", (int)a, (int)b);
                mult_result_ok2 = signed_mult(&mult_result2, a, b);
            }
            if (
                (mult_result_ok && mult_result_ok2) &&
                (mult_result != mult_result2)
                )
            {
                printf("mult(%d , %d)\n'", (int)a, (int)b);
                mult_result_ok2 = signed_mult(&mult_result2, a, b);
            }

        }     
    }
}



void unsigned_test()
{
    for (UNSIGNED_TYPE a = 0; a != UNSIGNED_MAX; a++)
    {
        for (UNSIGNED_TYPE b = 0; b != UNSIGNED_MAX; b++)
        {
            long long sub_result = ((long long)a) - ((long long)b);
            bool sub_result_ok = (sub_result >= UNSIGNED_MIN && sub_result <= UNSIGNED_MAX);

            long long add_result = ((long long)a) + ((long long)b);
            bool add_result_ok = (add_result >= UNSIGNED_MIN && add_result <= UNSIGNED_MAX);

            long long mult_result = ((long long)a) * ((long long)b);
            bool mult_result_ok = (mult_result >= UNSIGNED_MIN && mult_result <= UNSIGNED_MAX);


            UNSIGNED_TYPE sub_result2;
            bool sub_result_ok2 = unsigned_sub(&sub_result2, a, b);

            UNSIGNED_TYPE add_result2;
            bool add_result_ok2 = unsigned_sum(&add_result2, a, b);

            UNSIGNED_TYPE mult_result2;
            bool mult_result_ok2 = unsigned_mult(&mult_result2, a, b);



            if (sub_result_ok != sub_result_ok2)
            {
                printf("UNSIGNED_TYPE unsigned_sub(%d , %d)\n'", (int)a, (int)b);
                sub_result_ok2 = unsigned_sub(&add_result2, a, b);

            }
            if (
                (sub_result_ok && sub_result_ok2) &&
                (sub_result != sub_result2)
                )
            {
                printf("UNSIGNED_TYPE unsigned_sub(%d , %d)\n'", (int)a, (int)b);
                sub_result_ok2 = unsigned_sub(&add_result2, a, b);
            }


            if (add_result_ok != add_result_ok2)
            {
                printf("UNSIGNED_TYPE add(%d , %d)\n'", (int)a, (int)b);
                add_result_ok2 = signed_sum(&add_result2, a, b);

            }
            if (
                (add_result_ok && add_result_ok2) &&
                (add_result != add_result2)
                )
            {
                printf("UNSIGNED_TYPE add(%d , %d)\n'", (int)a, (int)b);
                add_result_ok2 = signed_sum(&add_result2, a, b);
            }


            if (mult_result_ok != mult_result_ok2)
            {
                printf("mult(%d , %d)\n'", (int)a, (int)b);
                mult_result_ok2 = unsigned_mult(&add_result2, a, b);
            }
            if (
                (mult_result_ok && mult_result_ok2) &&
                (mult_result != mult_result2)
                )
            {
                printf("mult(%d , %d)\n'", (int)a, (int)b);
                mult_result_ok2 = unsigned_mult(&add_result2, a, b);
            }

        }
    }
}

int main()
{

    signed_test();
    unsigned_test();

}

```

