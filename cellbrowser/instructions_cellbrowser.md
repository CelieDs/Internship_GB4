# Manual: how to deploy a Cell Browser of ovariancancercell.github.io

Authors: Célie Silva and Zhiyuan Hu

Date: 2020-06-26


## Installation of the cellbrowser

The best way to install the cellbrowser is by using pip. 

### Installation on Linux or Mac

On mac or Linux, just open the terminal and type : 

If you can run the sudo command : 
```
sudo pip install cellbrowser 
```

If you can’t run the sudo command : 
```
pip install --user cellbrowser
export PATH=$PATH:~/.local/bin ##(this command enable to export the right path for the cellbrowser)
```
### Installation on NoMachine

On the virtual machine provided by the University (NoMachine), you have to apply the following steps : 

```
Module avail (to see which modules are available)
Module load python3-cbrg
pip install --user cellbrowser
export ‘PATH=/home/obgynae/dcelie/.local/bin:$PATH’ >> ~/.profile (to export the right path to $PATH by editing your ~/.profile)
source ~/.profile (to actualize your profile)
```

### Installation on Windows 

If you want to develop the cellbrowser on Windows, you will have to install Linux Subsystem for Windows. 

* Open the control panel
* Go to programs 
* Click on “Turn Windows Features on or off”
* Select Windows Subsystem for Linux 

The installation will begin and at the end of it you will have to restart your computer. 

Then go to the Microsoft Store and download Ubuntu 18.04 LTS. Once you started the application, you will have to define your username and password. 
**/!\ Be careful to not forget them**

It is important to emphasise that Linux Subsystem for Windows is is really apart from your Windows system. If you install anything into Windows, it doesn't appear in the subsystem. 

This way, you will have to configure your Linux Subsytem. Go on the Ubuntu terminal and type : 

`sudo apt-get update`: to make sure all the links to the repositories are up to date

`sudo apt-get install python-pip`: to install python and pip 

You can check that python has well been installed by typing : `python --version`

`sudo pip install cellbrowser`: to install the cellbrowser

All the cellbrowser development will have to be made through the Linux terminal. 


## Configuration of the cellbrowser with text files

### Files required and file formats

The configuration of the cellbrowser with text files assumes that the clustering step has already been done.  

**Expression matrix** : one row per gene and one column per cell (gzipped if possible). 
* First column = gene identifiers or gene symbols or gene ID
* First line = header including the cell identifiers 


**Cell annotation metadata table** : one row per cell. 
* First column = name of the cell. The name of the cell has to match the name in the expression matrix
* Second column = name of the clusters

→ 2 columns are required (at least)

→ It is better if the expression matrix and the metadata file contain the same numbers of cells and have them in the same order (increasing the speed of cbBuild)


**Cell coordinates** : t-SNE, PCA or UMAP coordinates 
* First column = cellName. It must be the same as in the expression matrix and cell annotation metadata file
* Second column = x
* Third column = y 

→ If several dimensionality reduction algorithms have been done, we can specify multiple coordinate files in this format. 

→ The number of rows in this file doesn’t need to match the one in the two files above. 

→ If R has changed the cell identifiers we can fix them with :` cbTool metaCat`.


**Optional** : Cluster-specific marker genes : 

* First column = cluster name (from the cell annotation metadata file)
* Second column = gene symbol or Ensembl gene ID
* Third column = numeric score (e.g. p-value or FDR)

→ We can add as many columns as we want containing additional information on the gene

→ cbMarkerAnnotate : enables to add information from various gene-centric database and link-outs to other resources to our existing table


**!** All the files have to have the good line endings : \*.txt ; \*.csv ; \*.tsv ; \*.tab  (this can be done with `mac2unix`)


→ Example for a dataset : 
https://github.com/maximilianh/cellBrowser/blob/master/src/cbPyLib/cellbrowser/sampleConfig/cellbrowser.conf

   

## Organisation of the files and setting of the cellbrowser

All the files have to be in the same directory
  
  → run `cbBuild --init` in this directory (this command copy cellbrowser.conf and desc.conf in the directory)


If you are developing on Windows, you can access your Windows files with the command : 

`cd /mnt/c/Users/your_name/name_of_the_folder`


To set up your cellbrowser you have to modify two files : 
* cellbrowser.conf
* desc.conf


In the file cellbrowser.conf you will have to replace the default values in the config statements: 
* exprMatrix : expression matrix file name.
* meta : cell annotation metadata file name.
* coords : coordinate file names with a layout method label for each.
* markers : cluster specific marker gene file name with a label for each.
* labelfield and clusterfield : name of cluster field from header line of metadata file. 


You can also configure the following values: 
* name : internal short name of your data. You have to change this value only if you want your cellbrowser to have several tabs. Indeed, each tab will be * * characterized with the intern short name of the data it contains.
* shortLabel : name of your dataset
* shortLabel on coords : name of the reduction algorithm you use on your dataset. You can add as many reduction algorithm as you want, just add : 
```
{
        	"file":"cell_coordinates_tSNE.tsv", (name of the file containing the data)
        	"shortLabel":"t-SNE on Fallopian Tubes" (reduction algorithm used)
	}
```
On the ```coords=[...]``` part !
* quickgenes.csv : file containing the name of the gene of interest so that they can be easily accessible on the cellbrowser. 

In the file desc.conf you will have to add the abstract of your paper concerning the data, the methods used to obtain them, and other information (GEO accession number, personne submitting the paper…)

Then, from the directory where your cellbrowser.conf file is you can type :  

```cbBuild -o /tmp/cb -p 8888```


→ Point the internet browser on the name of the server (or localhost, if we are running it with our own machine : http://localhost:8888) to see it. 

→ `/tmp/cb` corresponds to the cellbrowser output directory : we can add multiple dataset by running the same command but we have to make sure that the intern name is different, as mentioned previously. 

→ `-o` is the command that enables you to define the output directory.


**!** On Windows, you can change the output directory to access easier your files. In addition, the folder created on the address /tmp/cb with Linux Subsystem for Windows doesn’t contain the html.files which is required to export your cellbrowser on the web. 

For example you can type `cbBuild -o /mnt/c/Users/your_name/cb` to obtain the output of the cellbrowser in your user files in a folder name cb, which will be created. 

→ You can access your Ubuntu output, at the following address : 
     
`C:\Users\your_username\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu18.04onWindows_79rhkp1fndgsc\LocalState\rootfs\tmp`


If you want to add several dataset on different tab on your cellbrowser, just run `cbBuild -o /output_directory_adress/cb` in the different folders containing your different files enabling the configuration of cellbrowser.conf. Of course you will have to run the command `cbBuild --init` in the beginning as well to obtain the files cellbrowser.conf and desc.conf.



## Exporting the cellbrowser on the Internet 

To export your cellbrowser on the internet just copy the output of the cbBuild function into an empty directory on a web server. 

You can use several web servers : 
* One provided by your university 
* Github Pages
* Commercial cloud providers such as Amazon S3 or Google cloud



## Maintaining the cellbrowser Ovarian Cancer

The cellbrowser Ovarian Cancer enables to visualise the data from the paper : The Repertoire of Serous Ovarian Cancer Nongenetic Heterogeneity Revealed by Single-Cell Sequencing of Normal Fallopian Tube Epithelial Cells published in February 2020. 


### Adding a dataset

To add the update on the website, you have to first clone the repository OvarianCancerCell.github.io, available in [the address]( https://github.com/OvarianCancerCell/OvarianCancerCell.github.io) as

```
git clone git@github.com:OvarianCancerCell/OvarianCancerCell.github.io.git
```

To add a dataset to the cellbrowser you need to clone the repository [cellBrowserOvarianCancer](https://github.com/OvarianCancerCell/cellBrowserOvarianCancer) or pull it if you have cloned before.

First clone the dataset repository to local (or simply go to the folder and pull with `git pull`)

```
git clone git@github.com:OvarianCancerCell/cellBrowserOvarianCancer.git
cd cellBrowserOvarianCancer
```

Then, add the folder containing the data you want to add. All the files have to follow the instruction presented above (in the part Configuration of the cellbrowser with text files, Files required and format). Please be aware that GitHub has 1GB storage limit for free-plan users. Update the dataset repository with

```
git commit -a 'add new dataset'
git push origin master
```

Then, run the command `cbBuild --init` in this folder, configure the two files cellbrowser.conf, and desc.conf (**! don’t forget to change the intern short name of the data**) as

```
cd newDataSet
cbBuild --init
```

Add keep the dataset repository up-to-date:

```
cd ..
git commit -a "update the repo"
git push origin master
```


Finally run the command `cbBuild -o /output_directory_adress/cb` in the different folders. If you modify an existing dataset, use

```
cd sampleSecretoryCells
cbBuild -o ../../OvarianCancerCell.github.io ## please modify this path
cd ..
```


If you add a new dataset, use

```
cd newDataSet
cbBuild -o ../../OvarianCancerCell.github.io 

cd ../..
```

Then you just have to push the new files to Github and the website will be actualised. 

```
cd OvarianCancerCell.github.io
git commit -a "update the repo"
git push origin master
```

### Modifying the dataset

To modify the dataset of the cellbrowser you can clone the repository cellBrowserOvarianCancer, available in [the address[(https://github.com/OvarianCancerCell/cellBrowserOvarianCancer)

Change what you want in the files cellbrowser.conf and desc.conf in the folder of the dataset. 
Build the new cellbrowser by running `cbBuild -o /output_directory_adress/cb` in the different folders.

Update the website, by cloning the repository OvarianCancerCell.github.io, available in [the address](https://github.com/OvarianCancerCell/OvarianCancerCell.github.io):

* Delete all the files that this repository contains and replace them with the files obtained from the cbBuild command in the cb folder. 
* Push the new files to Github (git push origin master) and the website will be actualised.


### Code manual for our instance

First set up your local github account as shown here: https://help.github.com/en/github/getting-started-with-github/set-up-git

In our instance, run the following code

```
git clone git@github.com:OvarianCancerCell/OvarianCancerCell.github.io.git

git clone git@github.com:OvarianCancerCell/cellBrowserOvarianCancer.git
cd cellBrowserOvarianCancer

cd sampleSecretoryCells
cbBuild -o ../../OvarianCancerCell.github.io ## please modify this path
cd ../fileFallopianTubeAll
cbBuild -o ../../OvarianCancerCell.github.io
# cd ../NewDatasetName
# cbBuild -o ../../OvarianCancerCell.github.io
cd ..

git commit -a "update the dataset" ## update the dataset repository
git push origin master

cd ../OvarianCancerCell.github.io
git commit -a "update the dataset on website" ## update the github page repository
git push origin master
```
