FROM frolvlad/alpine-glibc:latest as intermediate

LABEL MAINTAINER="Anaconda, Inc"
ENV CONDA_VERSION 4.5.12
LABEL SRC=https://github.com/ContinuumIO/docker-images/picoconda
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

ENV CONDA_MD5 866ae9dff53ad0874e1d1a60b1ad1ef8

# Create non-root user, install dependencies, install Conda
RUN addgroup -S anaconda && \
    adduser -D -u 10151 anaconda -G anaconda && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-$CONDA_VERSION-Linux-x86_64.sh && \
    echo "${CONDA_MD5}  Miniconda3-$CONDA_VERSION-Linux-x86_64.sh" > miniconda.md5 && \
    if [ $(md5sum -c miniconda.md5 | awk '{print $2}') != "OK" ] ; then exit 1; fi && \
    mv Miniconda3-$CONDA_VERSION-Linux-x86_64.sh miniconda.sh && \
    mkdir -p /opt && \
    sh ./miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh miniconda.md5 && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    chown -R anaconda:anaconda /opt && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> /home/anaconda/.profile && \
    echo "conda activate base" >> /home/anaconda/.profile && \
    . /opt/conda/bin/activate && conda clean -y --packages

# In theory `COPY --from=intermediate` starts us from a clean slate,
# but it resulted in much larger images than without for me.

FROM frolvlad/alpine-glibc:latest
COPY --from=intermediate /opt/conda /opt/conda

RUN addgroup -S anaconda && \
    adduser -D -u 10151 anaconda -G anaconda && \
    mkdir -p /opt && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    chown -R anaconda:anaconda /opt && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> /home/anaconda/.profile && \
    echo "conda activate base" >> /home/anaconda/.profile

USER  10151
ENV PATH "/bin:/sbin:/usr/bin"

CMD [ "sh", "--login", "-i" ]
