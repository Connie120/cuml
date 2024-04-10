SOURCEDIR= "/home/tgrogers-raid/a/kang222/cuml/cpp/bench/sg/"
# SOURCES := $(shell find $(SOURCEDIR) -name '*.cu')

LIBDIR= "/home/tgrogers-raid/a/kang222/cuml/cpp/src/"
LIBS+= $(shell find $(SOURCEDIR) -name '*.cuh')
LIBS+= $(shell find $(SOURCEDIR) -name '*.cu')
LIBS+= $(shell find $(SOURCEDIR) -name '*.h')

BENCHDIR= "/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/src"
SOURCES+= $(shell find $(BENCHDIR) -name '*.cc')
CUMLDIR= "/home/tgrogers-raid/a/kang222/cuml/cpp/src/common/"
SOURCES+= $(shell find $(CUMLDIR) -name '*.cpp')
SVMDIR= "/home/tgrogers-raid/a/kang222/cuml/cpp/src/svm"
SOURCES+= $(shell find $(SVMDIR) -name '*.cu')
SOURCES+= /home/tgrogers-raid/a/kang222/cuml/cpp/src/glm/glm.cu

ARCH=70

NVCCFLAGS= --expt-relaxed-constexpr --expt-extended-lambda
CFLAGS= --std=c++17 -DLIBCUDACXX_ENABLE_EXPERIMENTAL_MEMORY_RESOURCE=1

# arima.o: $(SOURCES)
# 	nvcc -gencode arch=compute_70,code=sm_70 sg/arima_loglikelihood.cu -I/home/tgrogers-raid/a/kang222/cuml/cpp/include -I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/raft-src/cpp/include/ -I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/rmm-src/include/ -I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/cccl-src/libcudacxx/include/ -I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/include/ -o $@

svr.o: $(SOURCES)
	nvcc $(NVCCFLAGS) $(CFLAGS) -gencode arch=compute_70,code=sm_70 $(SOURCES) sg/svr.cu /home/tgrogers-raid/a/kang222/cuml/cpp/src/svm/svr.cu -Xcompiler -fopenmp -lcublas -lcusolver -lcublasLt -lcusparse -I/home/tgrogers-raid/a/kang222/cuml/cpp/include \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/src/svm \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/src \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/src_prims \
	-I/home/tgrogers-raid/a/kang222/cuml/cuml-install/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cuml-install/include/libcudf/rapids \
	-I/home/tgrogers-raid/a/kang222/cuml/cuml-install/include/libcudf/rapids/libcudacxx \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/raft-src/cpp/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/cccl-src/libcudacxx/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/nvidiacutlass-src/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/cuco-src/include/ \
	-o $@

main.o: $(SOURCES)
	nvcc $(CFLAGS) -gencode arch=compute_70,code=sm_70 -dc $(SOURCES) -I/home/tgrogers-raid/a/kang222/cuml/cpp/include \
	-I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/include/ \
	-I/home/tgrogers-raid/a/kang222/cuml/cuml-install/include/ \
	-o $@

svr_dc: svr.o main.o 
	time nvcc $(CFLAGS) -gencode arch=compute_$(ARCH),code=sm_$(ARCH) main.o svr.o -I/home/tgrogers-raid/a/kang222/cuml/cpp/build/_deps/benchmark-src/include/ \
		-o svr_dc

# lib.o: $(LIBS) 
# 	nvcc -gencode arch=compute_70,code=sm_70 -dc $(LIBS) -I/home/tgrogers-raid/a/kang222/cuml/cpp -o lib.o

bench_dc: bench.o lib.o
	nvcc $(CFLAGS) --optimization-info inline -Xnvlink="-dump-callgraph-no-demangle" -gencode arch=compute_70,code=sm_70 bench.o lib.o -o bench_dc

clean:
	rm -f *.o
