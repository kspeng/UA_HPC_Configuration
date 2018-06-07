# Brief
This is a note for Ocelote GPU job allocation on HPC of University of Arizona. The detail instructions and flow are listed in this note.
	Reference: https://docs.hpc.arizona.edu/display/UAHPC/GPU+Nodes
	
# Interactive Mode
1. Request GPU resource.
	```
	qsub -I -N job_name -m  bea -W group_list=group_name -q windfall -l select=1:ncpus=28:mem=32gb:ngpus=1 -l cput=1:0:0 -l walltime=1:0:0
	```
	- job_name: assigned by user
	- group_name: NetId of user's sponsor
	- ncpus: requested number of cpu
	- mem: requested size of memory
	- ngpus: requested number of gpu
	- cput: maximum of 3200 hours total cpu time for 1-processor job
	- walltime: maximum of 240 hours walltime for the job
	
	ex. 
	```
	qsub -I -N monodepth -m  bea -W group_list=ditzler -q windfall -l select=1:ncpus=2:mem=32gb:ngpus=1 -l cput=240:0:0 -l walltime=240:0:0
	```
2. load singularity module 
	```
	module load singularity
	```
3. get into your workspace
	```
	cd ~/workspace/yourproj/
	```
4. run your proj within singularity img

	```
	singularity run --nv ~/workspace/envImg/recipe.img mycode.py
	```
