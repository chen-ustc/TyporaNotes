```c
#include<cuda_runtime.h>
#include<device_launch_parameters.h>
#include<stdio.h>
#include<time.h>
#include<stdlib.h>
#include<sys/timeb.h>
#include<windows.h>

double cpuSecond()
{
	return  clock();
}

__global__ void MatrixMul(double* Ad, double* Bd, double* Cd, int width)
{
	int tx = threadIdx.x;
	int ty = threadIdx.y;
	double sum = 0;
	double a, b;

	for (int k = 0; k < width; k++)
	{
		a = Ad[ty * width + k];
		b = Bd[k * width + tx];
		sum += a * b;
	}
	Cd[ty * width + tx] = sum;

}
int main()
{
	int dev = 0;
	cudaDeviceProp deviceProp;
	cudaGetDeviceProperties(&deviceProp, dev);
	printf("Using Device is %s\n", deviceProp.name);

	const int width = 5000;
	int size = width * width * sizeof(double);
	double* A, * B, * C;
	double* Ad, * Bd, * Cd;
	//double start, elaps;
	//struct timeb t1, t2;
	//SYSTEMTIME sys_start, sys_finish;
	//double t;
	clock_t start, finish;

	A = (double*)malloc(size);
	B = (double*)malloc(size);
	C = (double*)malloc(size);

	for (int i = 0; i < width * width; i++)
	{
		A[i] = 0.0;
		B[i] = 0.0;
	}

	cudaMalloc((void**)&Ad, size);
	cudaMalloc((void**)&Bd, size);
	cudaMalloc((void**)&Cd, size);



	int ilen = 128;
	dim3 block(ilen);
	dim3 grid((width * width + block.x - 1) / block.x);
	//start = cpuSecond();
	//ftime(&t1);
	start = clock();
	cudaMemcpy(Ad, A, size, cudaMemcpyHostToDevice);
	cudaMemcpy(Bd, B, size, cudaMemcpyHostToDevice);
	//GetLocalTime(&sys_start);
	//printf("%02dminute%02dsecond%02dms", sys_start.wMinute, sys_start.wSecond, sys_start.wMilliseconds);
	MatrixMul << <grid, block >> > (Ad, Bd, Cd, width);
	cudaMemcpy(C, Cd, size, cudaMemcpyDeviceToHost);
	//GetLocalTime(&sys_finish);
	//printf("%02dminute%02dsecond%02dms", sys_finish.wMinute, sys_finish.wSecond, sys_finish.wMilliseconds);
	//ftime(&t2);
	//elaps = cpuSecond() - start;
	finish = clock();

	//t = (double)(t2.time - t1.time) * 1000;
	printf("total time is  %lfs", ((double)(finish - start)/CLOCKS_PER_SEC));

	//for (int i = 0; i < width * width; i++)
	//{
	//	printf("%lf", C[i]);
	//}
	cudaFree(Ad);
	cudaFree(Bd);
	cudaFree(Cd);

	return 0;

}

//成功，暂存
```

