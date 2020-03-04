FROM ubuntu:18.04
RUN apt-get update
RUN apt-get install -y git
RUN apt-get install -y wget
RUN cd /root && git clone https://github.com/rezacsedu/Convolutional-embedded-networks.git
COPY pom_wee.xml /root/Convolutional-embedded-networks/PopulationClustering_v2/pom.xml
COPY jdk-8u241-linux-x64.tar.gz /usr/local/
RUN cd /usr/local && tar -xvzf jdk-8u241-linux-x64.tar.gz
#http://mirrors.ibiblio.org/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
ENV PATH /usr/local/jdk1.8.0_241/bin:${PATH}
COPY apache-maven-3.6.3-bin.tar.gz /usr/local/
RUN cd /usr/local && tar -xvzf apache-maven-3.6.3-bin.tar.gz
ENV PATH /usr/local/apache-maven-3.6.3/bin:${PATH}
RUN apt-get install -y vim
RUN cp -v /root/Convolutional-embedded-networks/PopulationClustering_v2/target/classes/META-INF/maven/com.deri.sels/PopulationClustering_v2/pom.xml  /root/Convolutional-embedded-networks/PopulationClustering_v2/pom.xml
RUN cd /root/Convolutional-embedded-networks/PopulationClustering_v2/ && mvn package
#COPY scala-2.13.1.tgz /usr/local/ 
#RUN cd /usr/local/ && tar -xvzf scala-2.13.1.tgz
#ENV PATH /usr/local/scala-2.13.1/bin:${PATH}
COPY scala-2.11.8.tgz /usr/local/
RUN cd /usr/local && tar -xvzf scala-2.11.8.tgz
ENV PATH /usr/local/scala-2.11.8/bin:${PATH}
COPY featureExtractor.scala /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics/
RUN  cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics/ && find *.scala | xargs -tI {} scalac -classpath /root/Convolutional-embedded-networks/PopulationClustering_v2/target/PopulationClustering_v2-0.1-SNAPSHOT-jar-with-dependencies.jar  {}
RUN cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics && wget http://hgdownload.cse.ucsc.edu/gbdb/hg19/1000Genomes/phase3/ALL.chrY.phase3_integrated_v1b.20130502.genotypes.vcf.gz && wget http://hgdownload.cse.ucsc.edu/gbdb/hg19/1000Genomes/phase3/ALL.chrY.phase3_integrated_v1b.20130502.genotypes.vcf.gz.tbi && gunzip -c ALL.chrY.phase3_integrated_v1b.20130502.genotypes.vcf.gz > ALL.chrY.phase3_integrated_v1b.20130502.genotypes.vcf
RUN cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics && gunzip -c ALL.chrY.phase3_integrated_v1b.20130502.genotypes.vcf.gz |  grep -P '^#CHROM' |tr "\t" "\n"|awk '{if(NR>=10) { print $0 }}' > genotypes.panel
RUN cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics && cat genotypes.panel | awk '{if(NR%2==0) { print "FIN" } else { print "GBR" }} ' > genotypes.panel.labels
RUN cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics && paste genotypes.panel genotypes.panel.labels > tmp.dat && cat tmp.dat > genotypes.panel
RUN  cd /root/Convolutional-embedded-networks/PopulationClustering_v2/src/main/scala/org/fit/genomics/ && scala -classpath /root/Convolutional-embedded-networks/PopulationClustering_v2/target/PopulationClustering_v2-0.1-SNAPSHOT-jar-with-dependencies.jar:. org.fit.genomics.featureExtractor
