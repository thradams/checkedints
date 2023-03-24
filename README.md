# checkedints

Checked integer arithmetic using KISS principle.


```c




#include <stdlib.h>
#include <assert.h>
#include <errno.h>
#include <stdio.h>
#include <limits.h>
#include <string.h>
#include <stdbool.h>


#define UNSIGNED_MAX ((unsigned int)UINT_MAX)
#define UNSIGNED_MIN ((unsigned int)0)

#define SIGNED_MAX ((signed int)INT_MAX)
#define SIGNED_MIN ((signed int)INT_MIN)

typedef unsigned int UNSIGNED_TYPE;
typedef signed int SIGNED_TYPE;

bool unsigned_sub(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b) {

    if (a < b)
        return 0;

    *result = a - b;
    return 1;
}

bool unsigned_mult(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b) {

    if (b == 0)
    {
        *result = 0;
        return true;
    }

    if (a > UNSIGNED_MAX / b)
        return 0;

    *result = a * b;
    return 1;
}

bool unsigned_sum(UNSIGNED_TYPE* result, UNSIGNED_TYPE a, UNSIGNED_TYPE b) {

    if (a > UNSIGNED_MAX - b)
        return 0;

    *result = a + b;
    return 1;
}


bool signed_sub(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) {
    if (a >= 0 && b >= 0) {
    }
    else if (a < 0 && b < 0) {
    }
    else {
        if (a < 0)
        {
            if (a < SIGNED_MIN + b)
                return false;
        }
        else
        {
            if (b == SIGNED_MIN)
                return false;
                
            if (a > SIGNED_MAX - (-b))
                return false;
        }
    }

    *result = a - b;
    return 1;
}

bool signed_sum(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) {

    if (a >= 0 && b >= 0) {
        /*both positive*/
        if (a > ((SIGNED_TYPE)SIGNED_MAX) - b)
            return 0;
    }
    else if (a < 0 && b < 0) {

        if (a == SIGNED_MIN || b == SIGNED_MIN)
            return 0;

        /*both negative*/


        if (a < ((SIGNED_TYPE)SIGNED_MIN) - b)
            return 0;

    }
    else {
        /*one positive another negative*/

    }

    *result = a + b;
    return 1;
}


bool signed_mult(SIGNED_TYPE* result, SIGNED_TYPE a, SIGNED_TYPE b) {


    if (a > 0 && b > 0) {
        /*both positive*/
        //a*b <= SIGNED_MAX
        if (a > SIGNED_MAX / b)
            return 0;
    }
    else if (a < 0 && b < 0) {

        //a*b <= SIGNED_MAX

        if (a == SIGNED_MIN || b == SIGNED_MIN)
        {
            return 0;
        }

        if (-a > SIGNED_MAX / -b)
            return 0;

    }
    else {
        if (a == 0 || b == 0)
        {
            *result = 0;
            return 1;
        }
        if (b > 0)
        {
            if (a < SIGNED_MIN / b)
                return 0;
        }
        else
        {
            if (b < SIGNED_MIN / a)
                return 0;
        }
    }

    *result = a * b;
    return 1;
}


void signed_test()
{
    printf("signed\n");
    for (SIGNED_TYPE a = SIGNED_MIN; a != SIGNED_MAX; a++)
    {
        for (SIGNED_TYPE b = SIGNED_MIN; b != SIGNED_MAX; b++)
        {
            long long sub_result = ((long long) a) - ((long long)b);
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
        printf("+\n");
    }
}



void unsigned_test()
{
    for (UNSIGNED_TYPE a = 0; a != UNSIGNED_MAX; a++)
    {
        for (UNSIGNED_TYPE b = 0; b != UNSIGNED_MAX; b++)
        {
            long long sub_result = ((long long)a) - ((long long)b);
            bool sub_result_ok = (sub_result >= SIGNED_MIN && sub_result <= SIGNED_MAX);

            long long add_result = ((long long)a) + ((long long)b);
            bool add_result_ok = (add_result >= SIGNED_MIN && add_result <= SIGNED_MAX);

            long long mult_result = ((long long)a) * ((long long)b);
            bool mult_result_ok = (mult_result >= SIGNED_MIN && mult_result <= SIGNED_MAX);


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

```
https://www.open-std.org/jtc1/sc22/wg14/www/docs/n2669.pdf
#include <stdckdint.h>
bool ckd_add(type1 *result, type2 a, type3 b);
bool ckd_sub(type1 *result, type2 a, type3 b);
bool ckd_mul(type1 *result, type2 a, type3 b);
```
