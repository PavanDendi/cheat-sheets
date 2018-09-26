## Jupyter Notebook secure server setup
From fresh install, generate a config file
```
jupyter notebook --generate-config
```
Relevant `jupyter_notebook_config.py` settings to expose notebook server
```
c.NotebookApp.ip = '0.0.0.0'
c.NotebookApp.port = 9999
c.NotebookApp.open_browser = False
```
### Enable HTTPS to encrypt connection
Generate self-signed cert and key files
```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
```
Generate password hash (using python)
```
In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'
```
Relevant `jupyter_notebook_config.py` settings
```
c.NotebookApp.password_required = True
c.NotebookApp.password = 'sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed'
c.NotebookApp.keyfile = u'/home/BRINKER/pdendi/jupyter/mykey.key'
c.NotebookApp.certfile = u'/home/BRINKER/pdendi/jupyter/mycert.pem'
```
## Apache Toree: Spark kernel for Jupyter Notebooks
As of 09/26/2018, Toree is still in incubating status.  Installation source will probably change with final release
```
pip install https://dist.apache.org/repos/dist/dev/incubator/toree/0.2.0-incubating-rc6/toree-pip/toree-0.2.0.tar.gz
```
Toree useful install options:  
`jupyter toree install -h` to see all options  
`--replace`&emsp;&emsp;Replace any existing kernel spec with this name.  
`--sys-prefix`&emsp;Install to Python's sys.prefix. Useful in conda/virtual environments.  
`--spark_home=`&emsp;Spark directory, must have subdir `python/lib`  
`--spark_opts=`&emsp;Specify command line arguments to proxy for spark config.

#### Actual command used to install on cluster
```
jupyter toree install --sys-prefix --spark_home=/opt/cloudera/parcels/SPARK2/lib/spark2
```
#### `SPARK_OPTS` environment variable will override --spark_opts values
Example:
```
$ SPARK_OPTS='--master=local[4]' jupyter notebook
```
### Cell Magics tutorial
https://github.com/apache/incubator-toree/blob/master/etc/examples/notebooks/magic-tutorial.ipynb