---
layout: post
title: "Personal debug/experiments workstation build"
excerpt: ""
tags: [Early dev/debug environment]
comments: true
---

ML developers can reduce cloud expenses by running proof of concept experiments and debug projects locally, using the same containerised environments that will be used to deploy the model.  

## The Hardware

Motherboard:    B550-A PRO MSI B550-A PRO AM4 ATX MOTHERBOARD  
DRAM:           CMK32GX4M2D3600C18 CORSAIR VENGEANCE LPX 32GB (2 X 16GB) DDR4 DRAM 3600MHZ C18 DESKTOP RAM - BLACK  
CPU:            100-100000065BOX AMD RYZEN 5 5600X 4.60GHZ 6C/12T AM4 DESKTOP PROCESSOR  
PSU:            SST-ST85F-PT SILVERSTONE STRIDER 80+ PLATINUM 850W POWER SUPPLY  
GPU:            MSI GEFORCE RTX 3060 VENTUS 2X 12G OC  
CASE:           CC-9011210-WW CORSAIR 5000D AIRFLOW TEMPERED GLASS MID-TOWER CASE - BLACK  
STORAGE:        MZ-77E1T0BW SAMSUNG 870 EVO 1TB 2.5" INTERNAL SSD, WD Black 2TB  
HCI:   		Keychron Q10 keyboard, Logitech MX ERGO mouse



## OS and Software  

OS:                     Ubuntu 22.04.1 LTS 64-bit  
NVIDIA driver:		525.85.12  
CUDA:			12.0  
Docker:			20.10.23  
Poetry:			1.3.2 (to assist in project config management)  
Text editors:           VS Code, Obsidian  


Now I can use the template repos in my GitHub account to initialise a project and maintain a stable deployment when the development work is complete.  
