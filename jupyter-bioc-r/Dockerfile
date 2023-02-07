FROM biodepot/r-bioc:4.2.2__ubuntu_22.04 
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get -y install firefox python3-all sudo python3-pip libfontconfig1-dev libharfbuzz-dev libfribidi-dev git libtiff-dev libzmq3-dev build-essential libgit2-dev \
 && python3 -m pip install pip==22.3.1 \
 && pip install jupyter \
 && R -e "install.packages(c('IRdisplay', 'devtools' ),repos = 'http://cran.us.r-project.org'); devtools::install_github('IRkernel/IRkernel'); IRkernel::installspec()" \
 && apt-get remove -y build-essential \
 && apt-get autoclean -y \
 && apt-get autoremove -y \
 && rm -rf /var/lib/apt/lists/*
COPY .mozilla /root/.mozilla

