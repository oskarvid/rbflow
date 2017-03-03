# Unofficial development location for the docker implementation of rbFlow

### How to set up and run the pipeline  
The startFromDocker.sh script assumes that a certain folder structure is in place, and it is recommended that you use the same folder structure unless you want to edit the script manually to suit your own preferences.  
The simplest way to get started is to clone this repository, change the file paths as described below, install Docker and then you're good to go!  

To install Docker you can follow the appropriate instructions on Dockers website for instructions: https://docs.docker.com/engine/installation/

To install the rbFlow Docker image, run 
```
sudo docker pull oskarv/rbflow:20161208
```

Before we run the docker image, we need to configure the startFromDocker.sh script.  
First of all, run 
```
sudo docker images
```
and copy the image ID of your "oskarv/rbflow" image, open startFromDocker.sh with a text editor and paste it into "IMAGEID=" at the top of the file.  

Scroll down and change the file paths at "# Paths" to point to your reference files and the rbflow folder.

The startFromDocker.sh script has divided the pre processing and germline variant calling steps into two separate scripts called preprocessing.yaml and germline_varcall.yaml. These scripts are stored in variables called "SCRIPT1" and "SCRIPT2" respectively. At the moment there is no way to automatically run the entire pipeline without needing to manually edit the startFromDocker.sh script under the "# For Running" comment. Thus, when you run the startFromDocker.sh script, it will run the pre processing, and to run the germline variant calling steps, you need to manually edit the startFromDocker.sh script and comment out this line: 
```
docker run -t --rm $CUST_USERID -v=$REFERENCE:/References -v=$DATA:/Data -w=/Data/ $IMAGE_ID sh -c "rbFlow.rb -c $SCRIPT1 -r"
```
and uncomment this line:
``` 
#docker run -t --rm $CUST_USERID -v=$REFERENCE:/References -v=$DATA:/Data -w=/Data/ $IMAGE_ID sh -c "rbFlow.rb -c $SCRIPT2 -r" 
```

As long as you run the startFromDocker.sh script from the "run" folder you should be ready to test the pipeline with the test data that is included in the "Samples" folder. Be adviced that the pipeline will crash on the final step in the germline_varcall.yaml workflow called "VariantRecalibration" due to too few samples in the test fastq files. As long as you have a complete set of fastq files the pipeline will finish successfully though.  
To start the pipeline, go to the terminal, change your working directory to the "run" directory and run 
```
sh startFromDocker.sh
```

### Cleaning the folder between runs  
If you run the clean.sh script, you will delete everything that gets created during an analysis.  

### Instructions for downloading the reference files for this pipeline
If you are part of a Norwegian university you have access to the NeLS portal, which is where we store the official reference files for this pipeline. If you have never used scp to download files from NeLS, watch [this tutorial](https://www.youtube.com/watch?v=TbUl8iuIwIw) for a guided walkthrough for how to download files from NeLS.  
Once you have your ssh private key file, you can copy and paste the code below, edit it and put in your NeLS username that you got in the tutorial. The NeLS file path should be the same as the one in the code below, but you need to edit the destination file path by changing "/your/destination" to your actual folder where the reference files will be located.

```
scp -r -i yourNeLSusername@nelstor0.cbu.uib.no.txt yourNeLSusername@nelstor0.cbu.uib.no:Projects/NCS-PM_Elixir_collaboration/Germline-varcall-wf-reference-files-v2.8/ /your/destination/
```

You can also download it using the graphical user interface if you prefer that, in that case you log in to NeLS and navigate to the "/Projects/NCS-PM_Elixir_collaboration" folder and click the zip file icon to download the entire folder as a zip file.

If you do not have access to NeLS you can use the official Broad Institute ftp site to download the reference files. Keep in mind that we cannot guarantee that the files from Broad Institute are identical to the ones we use, but there shouldn't be any difference. Use [this link](ftp://gsapubftp-anonymous@ftp.broadinstitute.org/bundle/) to access their ftp, you will be met by a login window, but just click "ok" because there's no password required.  
You're going to need to download these files manually:  
<details>
Some of the files in the list below have shorter names than the ones in the ftp directory, just choose the one that fits best, there's no risk of confusion.  
1000G_phase1.indels.b37.vcf  
1000G_phase1.indels.b37.vcf.idx  
1000g.vcf - This file is not available at their ftp site, you're going to need to find it some place else, it should be 6.9GB.  
1000g.vcf.idx  
dbsnp_138.b37.vcf  
dbsnp_138.b37.vcf.idx  
hapmap_3.3.b37.vcf.gz  
hapmap_3.3.b37.vcf.gzidx  
human_g1k_v37_decoy.dict  
human_g1k_v37_decoy.fasta  
human_g1k_v37_decoy.fasta.amb  
human_g1k_v37_decoy.fasta.ann  
human_g1k_v37_decoy.fasta.bwt  
human_g1k_v37_decoy.fasta.fai  
human_g1k_v37_decoy.fasta.pac  
human_g1k_v37_decoy.fasta.sa  
Mills_and_1000G_gold_standard.indels.b37.vcf  
Mills_and_1000G_gold_standard.indels.b37.vcf.idx  
omni.vcf - This file is not available at their ftp site, you're going to need to find it some place else, it should be 193 MB.  
omni.vcf.idx  
</details>
