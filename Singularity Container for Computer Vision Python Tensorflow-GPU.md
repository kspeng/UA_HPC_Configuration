## Brief
This note is a record of how to setup the Virtual Environment - Singularity Container for Computer Vision Python Tensorflow-GPU. The deployment platform is UofA HPC server. 

Reference: https://docs.hpc.arizona.edu/display/UAHPC/Singularity+Tutorials

## Steps

1. Install Singularity on linux workstation - http://singularity.lbl.gov/install-linux

2. Create the recipe file (.def file) on a workstation(linux only) with root authority. Let's call it recipe.def.

	**Content: keras+tensorflow_gpu-1.4.1-cp35-cuda8-cudnn6**
	- cuda8 & cuDnn6
	- python3.5
	- scipy
	- pandas
	- Pillow
	- matplotlib
	- opencv-python
	- keras
	- tensorflow 1.4.1
	
	**Flow**
	1) download cudnn6.0 for cuda8 (https://developer.nvidia.com/cudnn)
	2) prepare recipe.def including Computer Vision packages
	```
	BootStrap: yum
	OSVersion: 7
	MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
	Include: yum wget
	%setup
	   # commands to be executed on host outside container during bootstrap
	   cp ./packages/cudnn-8.0-linux-x64-v6.tgz $SINGULARITY_ROOTFS
	%test
	   # commands to be executed within container at close of bootstrap process
	   exec /usr/bin/python3.5 --version
	%runscript
	   # commands to be executed when the container runs
	   export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
	   echo "LD_LIBRARY_PATH: $LD_LIBRARY_PATH"
	   export PATH=/usr/local/cuda/bin:$PATH
	   echo "PATH: $PATH"
	   echo "Arguments received: $*"
	   exec /usr/bin/python3.5 "$@"
	%post
	   # commands to be executed inside container during bootstrap
	   yum -y install epel-release
	   yum -y install https://centos7.iuscommunity.org/ius-release.rpm
	   yum -y install http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-repo-rhel7-8.0.61-1.x86_64.rpm
	   # solve python opencv cv2 issue
	   yum -y install libSM-1.2.2-2.el7.x86_64 --setopt=protected_multilib=false 	
	   yum clean all && yum makecache
	   yum -y install wget python35u python35u-pip libgomp cuda-runtime-8-0
	   ln -s /usr/local/cuda-8.0 /usr/local/cuda
	   # install cudnn
	   tar -xf /cudnn-8.0-linux-x64-v6.tgz -C /usr/local
	   rm /cudnn-8.0-linux-x64-v6.tgz
	   # install tensorflow
	   pip3.5 install --upgrade pip
	   pip3.5 install --upgrade https://storage.googleapis.com/tensorflow/linux/gpu/tensorflow_gpu-1.4.1-cp35-cp35m-linux_x86_64.whl
	   pip3.5 install pandas 
	   pip3.5 install keras
	   pip3.5 install Pillow
	   pip3.5 install scipy 
	   pip3.5 install matplotlib
	   pip3.5 install opencv-python
	   # in-container bind points for shared filesystems
	   mkdir -p /extra /xdisk /uaopt /cm/shared
	```

3. Create the Singularity container using the recipe file. The image created was 1.5GB(recipe.img).
	```
	sudo singularity build recipe.img recipe.def
	```
	
4. sftp to hpc, put recipe.img into hpc.
	```
	sftp NetId@filexfer.hpc.arizona.edu 
	cd ~/workspace/envImg/
	put rescipe.img
	```
5. get into hpc server to run singularity image in interactive mode
	```
	ssh NetId@hpc.arizona.edu
	Ocelote
	cd ~/workspace/proj/
	# setup gpu resource
	qsub -I -N monodepth -m  bea -W group_list=ditzler -q windfall -l select=1:ncpus=28:mem=32gb:ngpus=1 -l cput=1:0:0 -l walltime=1:0:0
	# load singularity module
	module load singularity
	# run your proj within singularity img 
	singularity run --nv ~/workspace/envImg/recipe.img mycode.py
	```

