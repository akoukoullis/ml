FROM rocker/cuda-dev:3.5.2

RUN apt-get update && apt-get install -y --no-install-recommends \
    wget cmake && \
    git clone --recursive --branch v0.81 https://github.com/dmlc/xgboost && \
    mkdir -p xgboost/build && cd xgboost/build && \
    cmake .. -DUSE_CUDA=ON -DR_LIB=ON -DUSE_NCCL=ON && \
    make install -j$(nproc)

FROM rocker/tensorflow-gpu:3.5.2

# Python Xgboost for CPU
RUN pip3 install \
    wheel==0.33.0 \
    setuptools==40.8.0 \
    scipy==1.2.1 \
    tensorflow-probability==0.5.0 --upgrade

## xgboost with multi-GPU support
RUN apt-get update && apt-get -y install wget && \
  wget https://s3-us-west-2.amazonaws.com/xgboost-wheels/xgboost-0.81-py2.py3-none-manylinux1_x86_64.whl && \
  pip3 install  xgboost-0.81-py2.py3-none-manylinux1_x86_64.whl && \
  rm xgboost-0.81-py2.py3-none-manylinux1_x86_64.whl && \
  apt-get remove --purge -y wget && \
  rm -rf /var/lib/apt/lists/*

COPY --from=0 /usr/local/lib/R/site-library/xgboost /usr/local/lib/R/site-library/xgboost

# Get Java (for h2o R package)
RUN apt-get update -qq && \
    apt-get -y --no-install-recommends install \
      default-jdk \
      default-jre && \
  	R CMD javareconf

## h2o requires Java
RUN install2.r h2o
RUN install2.r greta

