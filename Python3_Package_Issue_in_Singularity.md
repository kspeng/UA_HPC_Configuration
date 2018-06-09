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
