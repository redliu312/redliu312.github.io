---
title : How to cross compile windows and linux , 32 and 64 bit executable 
category : Cross compile
tags: [C, cross compile,python,docker,dockcross]
---


How do I cross compile windows and Linux 32 bit and 64 bit application on a centos  x86-64 machine

# **Objective**

For the current job , I need to maintain and release a application written in C language. We build this applicaton with glibc and we also port it to windows by mingw defined in the code.  Now we have four versions of the application needed to be compiled and put it to the release archive.  When I took over this project, the old workflow of my colleague was buid the winodows versions by  mingw64 and mingw32, and he need to borrow linux 64 bit  and 32bit  machine from our SIT. I make a little improvement on this work flow by installing virtual machines on my windows laptop. But when the function upgrading of the application required by our customer happened more and more frequently, the time I spend on the building and release make me feel depressed.
I want to make a improvement to this work flow to make it will only need one OS, and one script .




# **Environment**

This is an virtualbox guest installed on my windows laptop.


    CentOS release 6.8 (Final)
    Linux localhost.localdomain 2.6.32-642.11.1.el6.x86_64 #1 SMP Fri Nov 18 19:25:05 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

# **How**

## **Linux version**

The only thing I need to installed is the gcc i686 develop tools which are required for me to build a 32 bit linux applications.

    sudo yum install libgcc.i686 glibc-devel.i686 glibc.i686 

Now when I want to use the gnu make to build the 32 bit application,
we only need to use 

    make CFLAGS=-m32 all

no mentioned on the  64 bit machine

    make all

## **Windows version**

I use a docker image to build the windows applications.This image are on the [github](https://github.com/dockcross/dockcross).I use the windows-x86 and windows-x64 for my purpose.This docker image is very easy to use which it supply a way to execute it just like a normal bin in linux. When the setting(follow the instructions on the github) is done, I can build windows executables with the commands below:  

32 bit

    dockcross-windows-x64 make  all

64 bit

    dockcross-windows-x86 make  all




# **Life is short , use python**

Now I have four command to execute for my final release archive. I wrote a python script which use multiprocessing work in different work folder and build the four versions  executables and pass the executable to the release folder , surely compress the release folder for me in the final.
Here is mt python script code:

```python
    #!/usr/bin/env python
    import time
    import subprocess
    import os
    import shutil
    import multiprocessing
    
    def Make(buildobj):
    
        # print(self.resource_obj.value)
        # self.resource_obj.acquire()
        buildobj.copySourceCodeFromShare()
        buildobj.moveToSourceCodeDir()
        buildobj.buildTheFuckingCode()
        buildobj.moveExecToReleaseFolder()
        # self.resource_obj.release()
    
    
    class builder(object):
        """docstring for ClassName"""
    
        def __init__(self, version, buildcommand, buildPath,
                     sourceCodePath, releaseFolder):
            self.buildcommand = buildcommand
    
            self.buildPath = buildPath
            self.sourceCodePath = sourceCodePath
            self.version = version
            self.releaseFolder = releaseFolder
    
        def copySourceCodeFromShare(self):
            src = "/mnt/sharefolder/AppName_wl"
            des = "/root/crossCompile/AppName_wl" + self.version
            des = os.path.abspath(des)
            if os.path.exists(des):
                shutil.rmtree(des)
    
            shutil.copytree(src, des)
            self.buildPath = des
    
        def moveToSourceCodeDir(self):
            os.chdir(self.buildPath)
    
        def buildTheFuckingCode(self):
            print(os.getcwd())
            # print(self.threadID)
            self.makeClean()
            output = subprocess.check_output(
                self.buildcommand, shell=True, stderr=subprocess.STDOUT)
            print(output)
    
        def moveExecToReleaseFolder(self):
            AppName = self.renameAppName()
            releaseDes = self.releaseFolder + self.version + "/"
            releaseDes = os.path.abspath(releaseDes)
            if not os.path.exists(releaseDes):
                os.makedirs(releaseDes)
    
            shutil.copy(os.path.abspath(AppName), os.path.abspath(releaseDes))
            self.makeClean()
    
        def renameAppName(self):
            tailFix = ""
            if 'win' in self.version:
                tailFix += ".exe"
            # rename = 'AppName_'+ self.version + tailFix
            rename = 'AppName' + tailFix
            os.rename('AppName', rename)
    
            return rename
    
        def makeClean(self):
            print(os.getcwd())
            # print(inspect.currentframe().f_code.co_name)
            cmd = 'make clean'
            subprocess.check_output(cmd, shell=True, stderr=subprocess.STDOUT)
    
    
    def main():
    
        releaseFolder ="/mnt/sharefolder/AppNameRelease/"
        linux32_builder = builder(
            "linux32", "make -j2 CFLAGS=-m32 all", "./", "./", releaseFolder)
    
        linux64_builder = builder(
            "linux64", "make -j2  all", "./", "./", releaseFolder)
    
        win64_builder = builder(
            "win64", "dockcross-windows-x64 make -j2 all", "./", "./", releaseFolder)
        win32_builder = builder(
            "win32", "dockcross-windows-x86 make -j2 all", "./", "./", releaseFolder)
    
        # linux32_builder.copySourceCodeFromShare()
        Worker = []
        Worker.append(linux32_builder)
        Worker.append(linux64_builder)
        Worker.append(win64_builder)
        Worker.append(win32_builder)
    
        # Make(Worker[3])
        pool = multiprocessing.Pool(processes=4)
        result = []
        #for i in range(len(Worker)):
            #result.append(pool.apply_async(Make, (Worker[i], )))    `
        result.append(pool.apply_async(Make, (Worker[0], )))
        result.append(pool.apply_async(Make, (Worker[2], )))
        result.append(pool.apply_async(Make, (Worker[1], )))
        result.append(pool.apply_async(Make, (Worker[3], )))
        pool.close()
        pool.join()
        for res in result:
            print res.get()
        print "Sub - process(es) done."
    
        zipfilename=shutil.make_archive(releaseFolder,"zip","/mnt/sharefolder/","AppNameRelease")
        # newzipname = "/root/crossCompile/releaseFolder/AppNameRelease.zip"
        # shutil.move(zipfilename,newzipname)
        # shutil.copy(newzipname,"/mnt/sharefolder/")
    if __name__ == '__main__':
        main()
```    


