CU_APPS=nestedHelloWorld nestedReduce nestedReduce2 nestedReduceNosync \
	    reduceInteger simpleDeviceQuery simpleDivergence sumMatrix
C_APPS=

all: ${C_APPS} ${CU_APPS}

%: %.cu
	nvcc -g -G -o $@ $< -lcudadevrt --relocatable-device-code true
#	nvcc -O2 -arch=sm_35 -o $@ $< -lcudadevrt --relocatable-device-code true
%: %.c
	gcc -g -G -std=c99 -o $@ $<
clean:
	rm -f ${CU_APPS} ${C_APPS}
