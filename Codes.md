Less complex than CUDA or OpenCL for basic parallel tasks
program code: gedit openmp.c
#include <stdio.h>
Dept Of ISE 2025-26
Page 33
PARALLEL COMPUTING [MVJ22IS72]
#include <stdlib.h>
#include <openacc.h>
#define N 1000
int main() {
int i;
float a[N], b[N], c[N];
// Initialize arrays
for (i = 0; i < N; i++) {
a[i] = i * 1.0f;
b[i] = i * 2.0f;
}
// Parallel region - use OpenACC to compute c[i] = a[i] + b[i]
#pragma acc parallel loop
for (i = 0; i < N; i++) {
c[i] = a[i] + b[i];
}
// Print some results to verify
printf("Sample Output:\n
");
for (i = 0; i < 10; i++) {
printf("
c[%d] = %f\n
"
, i, c[i]);
}
return 0;
}
Compilation:
kalyani@DESKTOP-9HEJFCC:~/Lab5$ mpicc openmp.c -o openmp
Run:
kalyani@DESKTOP-9HEJFCC:~/Lab5$ mpirun -np 4 ./openmp
Output:
c[0] = 0.000000
c[1] = 3.000000
c[2] = 6.000000
c[3] = 9.000000
c[4] = 12.000000
c[5] = 15.000000
c[6] = 18.000000
c[7] = 21.000000
c[8] = 24.000000
