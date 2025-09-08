Program Code: $]gedit lab3f.c
#include <mpi.h>
#include <stdio.h>
int main(int argc, char** argv) {
MPI_Init(&argc, &argv);
int rank;
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
MPI_File fh;
MPI_File_open(MPI_COMM_WORLD,
"
output.txt"
, MPI_MODE_CREATE |
MPI_MODE_WRONLY, MPI_INFO_NULL, &fh);
char buf[50];
sprintf(buf,
"Hello from process %d\n
"
, rank);
MPI_Offset offset = rank * 50;
MPI_File_write_at(fh, offset, buf, 50, MPI_CHAR, MPI_STATUS_IGNORE);
MPI_File_close(&fh);
MPI_Finalize();
return 0;
}
Compile and run: mpicc lab3f.c -o lab3f
mpirun -2 4 ./lab3f
Output: output.txt file will be generated with the following output. To open the
file , execute the following command
cat output.txt
Hello from process 0
Hello from process 1
