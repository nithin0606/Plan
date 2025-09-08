code 
'''java 
Program Code: $]gedit add.c
#include <stdio.h>
#include <omp.h>
int main() {
int i;
const int N = 1000;
int array[N];
long long sum = 0;
// Initialize array
for(i = 0; i < N; i++) {
array[i] = 1; // All elements are 1
}
// Parallel region for sum using reduction
#pragma omp parallel for reduction(+:sum)
for(i = 0; i < N; i++) {
sum += array[i];
}
printf("Sum of array elements = %lld\n
"
, sum);
return 0;
}
Compilation: gcc -fopenmp add.c -o add
Run: ./add
