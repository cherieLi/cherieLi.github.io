---
layout: post
title:  在gem5上配置和运行PARSEC 2.1数据集
date:   2018-03-27 17:46:45 +0800
categories: linux
tags: gem5
author: CherieLi
---

本篇介绍如何在GEM5模拟器中配置和运行PARSEC Benchmark （以ARPHA架构方式为例）。PARSEC Benchmark需要在GEM5中的全系统（full system）模式下运行，相关教程可参考：[http://www.m5sim.org/PARSEC_benchmarks](http://www.m5sim.org/PARSEC_benchmarks) .

1. 首先新建一个文件夹用于存储PARSEC Benchmark的disk image

        mkdir full_system_images
        cd full_system_images

2. 下载初始的系统文件，并解压，再重命名文件夹（重命名可选）

        wget http://www.m5sim.org/dist/current/m5_system_2.0b3.tar.bz2
		tar jxf m5_system_2.0b3.tar.bz2
	    mv m5_system_2.0b3 system
	
	解压后，文件的目录结构如下：
	    
		system/
            binaries/
                 console
                 ts_osfpal
                 vmlinux
            disks/
                 linux-bigswap2.img
                 linux-latest.img
				 
3. 下载PARSEC Benchmark相关文件，并替换掉system文件夹中的相应文件

    下载PARSEC对应的linux kernel文件，并替换掉 'system/binaries/vmlinux'
	    
		cd ./system/binaries/
	    wget http://www.cs.utexas.edu/~parsec_m5/vmlinux_2.6.27-gcc_4.3.4
	    rm vmlinux
		mv vmlinux_2.6.27-gcc_4.3.4 vmlinux
		
	下载PARSEC对应的PAL code文件， 并替换掉 'system/binaries/ts_osfpal'
	
	    wget http://www.cs.utexas.edu/~parsec_m5/tsb_osfpal
		rm ts_osfpal
		mv tsb_osfpal ts_osfpal
		
	下载PARSEC-2.1 Disk Image并解压
	
	    cd ../disks/
		wget http://www.cs.utexas.edu/~parsec_m5/linux-parsec-2-1-m5-with-test-inputs.img.bz2
		bzip2 -b linux-parsec-2-1-m5-with-test-inputs.img.bz2
		
4. 进入gem5文件夹，修改两个文件（SysPaths.py 和 Benckmarks.py）配置parsec的路径和文件名

		cd ..
		
		cd configs/common/

     打开SysPaths.py配置parsec disk image的完整路径：
	
	    vim SysPaths.py
	
	修改前：
	
        path = [ ’/dist/m5/system’, ’/n/poolfs/z/dist/m5/system’ ]
	
	修改后：
	
        paths = [ '/dist/m5/system', '/home/lzy/gem5/gem5/full_system_images/system' ]
	
	打开Benchmarks.py，修改image文件名：
	
	    vim ./configs/common/Benchmarks.py
	
	修改前：
	
        elif buildEnv['TARGET_ISA'] == 'alpha':
            return env.get('LINUX_IMAGE', disk('linux-latest.img'))
			
	修改后：
	
        elif buildEnv['TARGET_ISA'] == 'alpha':
            return env.get('LINUX_IMAGE', disk('linux-parsec-2-1-m5-with-test-inputs.img'))
			
5. 生成benchmark的script文件，用于运行benchmark

    下载PARSEC script生成包，并解压：
	
	    wget http://www.cs.utexas.edu/~parsec_m5/TR-09-32-parsec-2.1-alpha-files.tar.gz
		tar zxvf TR-09-32-parsec-2.1-alpha-files.tar.gz
		
    生成script命令：
		mv TR-09-32-parsec-2.1-alpha-files PARSEC

		cd PARSEC

	    ./writescripts.pl <benchmark> <nthreads>
		eg:  ./writescripts.pl canneal 4
		
	有以下13种benchmark：
	
	    blackscholes
	    bodytrack
	    canneal
	    dedup
	    facesim
	    ferret
	    fluidanimate
	    freqmine
	    streamcluster
	    swaptions
	    vips
	    x264
	    rtview
		
6. 根据生成的script文件运行gem5：

        ./build/ALPHA/gem5.opt ./configs/example/fs.py -n 4 --script=/home/lzy/gem5/gem5/parsec/canneal_4c_simsmall.rcS --caches --l2cache -F 5000000000
	
	得到如下结果：  
	Global frequency set at 1000000000000 ticks per second  
	warn: DRAM device capacity (8192 Mbytes) does not match the address range assigned (512 Mbytes)  
	info: kernel located at: /home/lzy/gem5/gem5/full_system_images/system/binaries/vmlinux  
	Listening for system connection on port 3456  
	......

7. 新开一个窗口，使用telnet与gem5模拟系统进行交互

        telnet localhost 3456  
	
	得到如下结果：  
	......  
	PARSEC Benchmark Suite Version 2.1  
	[HOOKS] PARSEC Hooks Version 1.2  
	Threadcount: 4  
	10000 swaps per temperature step  
	start temperature: 2000  
	netlist filename: /parsec/install/inputs/canneal/100000.nets  
	number of temperature steps: 32  
	locs created  
	locs assigned  
	Just saw element: 100000  
	netlist created. 100000 elements.  
	[HOOKS] Entering ROI  
	[HOOKS] Leaving ROI  
	Final routing is: 9.27137e+07  
	[HOOKS] Total time spent in ROI: 0.072s  
	[HOOKS] Terminating  
	Done :D

        
