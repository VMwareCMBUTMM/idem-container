FROM photon:3.0
LABEL maintainer="smcgeown@vmware.com"

ENV PYTHON_VERSION=python3-3.7.5-9.ph3
ENV PIP_VERSION=python3-pip-3.7.5-9.ph3
ENV IDEM_VERSION=15.0.0
ENV IDEM_AZURE_AUTO_VERSION=0.0.3

RUN tdnf install ${PYTHON_VERSION} ${PIP_VERSION} -y && \
        python3 -m venv idem && \
        source idem/bin/activate && \
        pip3 install --upgrade pip && \
        pip3 install --upgrade setuptools && \
        pip3 install idem==${IDEM_VERSION} idem-azure-auto==${IDEM_AZURE_AUTO_VERSION}
        
ENV PATH="/idem/bin:$PATH"

ENTRYPOINT [ "idem" ]