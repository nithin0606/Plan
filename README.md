# Plan

Program Code: lab3a.c
#include <mpi.h>
#include <stdio.h>
int main(int argc, char** argv) {
MPI_Init(&argc, &argv);
int rank;
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
int number;
if (rank == 0) {
number = 42;
MPI_Send(&number, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
} else if (rank == 1) {
MPI_Recv(&number, 1, MPI_INT, 0, 0, MPI_COMM_WORLD,
MPI_STATUS_IGNORE);
printf("Process 1 received number %d from Process 0\n
"
, number);
}
MPI_Finalize();
return 0;
}
Compile and run:
mpicc lab3a.c -o lab3a
mpirun -np 2 ./lab3a
Output:
Process 1 received number 42 from Process 0
Program b) Explanation of lab3b:
It is a simple Python MPI program using the mpi4py library. It demonstrates basic
point-to-point communication between two processes.
purpose of the Program:
To send a number (42) from Process 0 to Process 1 using MPI in Python.
This example helps students understand:
MPI concepts in Python
How to send and receive messages between processes

Imports the mpi4py module, which allows us to use MPI functions in Python.
comm = MPI.COMM_WORLD
COMM_WORLD is the default communicator that includes all MPI processes.
comm is used to send and receive messages between these processes.
rank = comm.Get_rank()
Retrieves the rank (ID) of the current process in COMM_WORLD.
If you
'
re running with 2 processes, rank will be either 0 or 1.
conditional Code Based on Rank:
if rank == 0:
data = 42
comm.send(data, dest=1, tag=0)
If the process is rank 0, it:
Sets data = 42
Sends this value to rank 1 using comm.send
dest=1: destination process
tag=0: a label to identify the message
elif rank == 1:
data = comm.recv(source=0, tag=0)
If the process is rank 1, it:
Receives the message from rank 0 using comm.recv
source=0: the sending process
tag=0: must match the tag used in send
print(f"Process 1 received data: {data}")
All processes run this line.
Term Meaning
MPI.COMM_WORLD Default communicator including all processes
comm.Get_rank() Gets the process ID (rank)
comm.send() Sends data to another process
comm.recv() Receives data from another process
tag Identifier for matching messages
Program Code: $]lab3b.py
from mpi4py import MPI
comm = MPI.COMM_WORLD
rank = comm.Get_rank()
if rank == 0:
data = 42
comm.send(data, dest=1, tag=0)
elif rank == 1:
data = comm.recv(source=0, tag=0)
print(f"Process 1 received data: {data}")
RUN in terminal:
mpirun -np 2 python3 lab3b.py
/*Visually
Process 0 Process 1
---------- 


C (Broadcast)
Explanation : lab3d.c
C MPI program which demonstrates the Broadcast (Bcast) collective
communication in MPI.
Purpose of the Program:
To send the same data (100) from one process (rank 0) to all other processes using
MPI_Bcast.
#include <mpi.h>
#include <stdio.h>
Include necessary headers for using MPI functions and standard input/output.
int main(int argc, char** argv) {
MPI_Init(&argc, &argv);
Initialize the MPI environment. This must be called before any other MPI functions.
int rank, data;
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
rank stores the unique ID of the current process.
If 4 processes are launched, ranks will be 0, 1, 2, and 3.
if (rank == 0) data = 100;
Only process 0 sets the value of data = 100.
MPI_Bcast(&data, 1, MPI_INT, 0, MPI_COMM_WORLD);
Broadcasts the value of data from process 0 to all other processes.
After this line, every process (even non-zero ranks) will have data = 100.
&data: address of the data being shared
1: number of items
MPI_INT: data type
0: root process (sender)
MPI_COMM_WORLD: communicator (all processes)
printf("Process %d received data %d\n
"
, rank, data);
Each process prints the value it received.
MPI_Finalize();
return 0;
Shuts down the MPI environment. All processes must call this before exiting.
Output (if 4 processes are used):
Process 0 received data 100
Process 1 received data 100
17
Dept of ISE


MPI_Bcast Sends the same data from one root process to all other processes
MPI_COMM_WORLD Default group of all processes
rank Unique ID assigned to each process
Program Code: vi lab3d.c
#include <mpi.h>
#include <stdio.h>
int main(int argc, char** argv) {
MPI_Init(&argc, &argv);
int rank, data;
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
if (rank == 0) data = 100;
MPI_Bcast(&data, 1, MPI_INT, 0, MPI_COMM_WORLD);
printf("Process %d received data %d\n
"
, rank, data);
MPI_Finalize();
return 0;
}
Compilation:
mpicc lab3d.c -o lab3d
Run: mpirun -np 2 ./lab3d
Output:
Process 0 received data 100
Process 1 received data 100


lab3e.py
from mpi4py import MPI
comm = MPI.COMM_WORLD
rank = comm.Get_rank()
data = None
if rank == 0:
data = 100
data = comm.bcast(data, root=0)
print(f"Process {rank} received data: {data}")
Run the program : mpirun -np 2 python3 lab3e.py
Output:
Process 0 received data: 100
Process 1 received data: 100
Python (gather):
Explanation gather_example.py:
To demonstrate how the gather() function is used to collect data from multiple
processes into a single root process (usually rank 0).
MPI Operation Purpose
gather() Collects data from all processes to a single root process
from mpi4py import MPI
Imports the MPI module in Python. This enables us to use MPI functions like
gather().
comm = MPI.COMM_WORLD
COMM_WORLD is the default communicator that includes all the processes
involved in the MPI program.
rank = comm.Get_rank()
Each process gets its unique ID, called rank.
If 4 processes are running, ranks will be 0, 1, 2, 3.
size = comm.Get_size()
Returns the total number of processes running in the communicator.

Each process calculates the square of its rank.
Process 0 → 0 ** 2 = 0
Process 1 → 1 ** 2 = 1
Process 2 → 2 ** 2 = 4
Process 3 → 3 ** 2 = 9
gathered_data = comm.gather(send_data, root=0)
Collects send_data from all processes and sends it to root process (rank 0).
The root receives a list of data: [0, 1, 4, 9].
if rank == 0:
print(f"Process {rank} gathered data from all processes: {gathered_data}")
Only rank 0 prints the result.
The gathered list shows data from all processes in order of their ranks.
Output (when run with 4 processes):
Process 0 gathered data from all processes: [0, 1, 4, 9]
Visual Representation:
[Process 0] send_data = 0 \
[Process 1] send_data = 1 \
[Process 2] send_data = 4 [Process 3] send_data = 9 /
(collected by root process)
====> gathered_data = [0, 1, 4, 9]
MPI ConceptDescription
comm.gather() Collects data from all processes to the root process
rank Unique ID for each process
root=0 The process that will collect and store all gathered data
How to Run This Program:
Save as gather_example.py, and run with 4 processes:
mpiexec -n 4 python gather_example.py
Program Code: $]gather_example.py
from mpi4py import MPI
20
Dept of ISE

comm = MPI.COMM_WORLD
rank = comm.Get_rank()
size = comm.Get_size()
# Each process creates a value (e.g., its rank squared)
send_data = rank ** 2
# Root process will collect data from all
gathered_data = comm.gather(send_data, root=0)
if rank == 0:
print(f"Process {rank} gathered data from all processes: {gathered_data}")
How It Works:
Each process computes rank ** 2.
comm.gather() sends this value from each process to the root process (rank 0).
Only rank 0 receives the full list of gathered data.
Save as gather_example.py and run using:
mpiexec -n 4 python gather_example.py
Output (for 4 processes):
Process 0 gathered data from all processes: [0, 1, 4, 9]
Explanation:
Process 0 sends 0**2 = 0
Process 1 sends 1**2 = 1
Process 2 sends 2**2 = 4
Process 3 sends 3**2 = 9
The root (rank 0) collects: [0, 1, 4, 9]
Python (scatter):
Scatter: simple and clear Python MPI program using mpi4py that demonstrates the
Scatter collective communication pattern, along with the expected output when
run with 4 processes.
MPI Operation Purpose
scatter() Splits a list into parts and distributes each part to each process
Python MPI program using mpi4py that demonstrates the Scatter operation.
Purpose of the Program:
To demonstrate how a list of values can be divided and distributed among multiple
processes using comm.scatter().

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

Each message occupies exactly 50 bytes, which ensures non-overlapping parallel
writes.
Program code of parallel_io.py
from mpi4py import MPI
comm = MPI.COMM_WORLD
rank = comm.Get_rank()
fh = MPI.File.Open(comm,
"
output_py.txt"
, MPI.MODE_CREATE |
MPI.MODE_WRONLY)
data = f"Hello from process {rank}\n
"
.ljust(50)
offset = rank * 50
fh.Write_at(offset, data.encode())
fh.Close()
mpirun -np 2 python3 lab3g.py
28
Dept of ISE 2025-2026
Output: output_py.txt file will be generated with the following output. To open the
file , execute the following command
cat output_py.txt
Hello from process 0
Hello from process 1




---

AIM:
Programming in OpenMP.
Explanation of add.c :
OpenMP parallel sum program in C:
Purpose of the Program:
Calculate the sum of all elements in an array using parallel programming
with OpenMP to speed up the summation.
#include <stdio.h>
#include <omp.h>
Include standard input/output and OpenMP libraries for parallel programming.
int main() {
int i;
const int N = 1000;
int array[N];
long long sum = 0;
Declare variables:
N = 1000 is the size of the array.
array[N] is an integer array of size 1000.
sum is a variable to store the final sum, declared as long long to hold large values.
// Initialize array
for(i = 0; i < N; i++) {
array[i] = 1; // All elements are 1
}
Initialize the array: each element is set to 1.
// Parallel region for sum using reduction
#pragma omp parallel for reduction(+:sum)
for(i = 0; i < N; i++) {
sum += array[i];
}
#pragma omp parallel for reduction(+:sum):
This is an OpenMP directive that tells the compiler to:
Run the for loop in parallel across multiple threads.
Use reduction on sum to safely accumulate values from all threads.

---

include <stdio.h>
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
Dept Of ISE

---

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
