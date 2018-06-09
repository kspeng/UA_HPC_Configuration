# Brief
There are some existed Python3.5 Package issue in Singularity Container. This note records the known issues and known work around.

# Issues
### matplotlib: ImportError: No module named 'tkinter'
There is no way to install python-tkinter for python3 in Singularity Container as I know. When we import matplotlib.pyploy, an ImportError of 'tkinter' occurs. The problem is no agg backend to support matplotlib. The work around is to use 'agg' right after import matplotlb.
  ```
  import matplotlib
  matplotlib.use('Agg')
  import matplotlib.pyplot as plt
  ```
### opencv-python: ImportError: No module named 'cv'
When install opencv-python for python3.5 in th container.edf, we must incluse following line to solve link issue:
  ```
  yum -y install libSM-1.2.2-2.el7.x86_64 --setopt=protected_multilib=false 
  ```
Then we can use opencv correctly. However, the cv is not included and it always shows no module named 'cv'. So far, the only way is import cv2 without cv.
  ```
  import cv2
  ```  
