

<h1 align="center">CAS771 Project</h1>

In this project, we combine the nested dropout method and co-teaching method to deal with noisy label datasets.

- [How to use it?](#how-to-use-it)
- [References](#references)

## How to use it?
Firstly, you can process dataset and generate different noisy type and level by following command:

1. Pre-process CIFAR-10 and CIFAR-100 dataset
   
   Firstly, put CIFAR-10/CIFAR-100 in Process_date folder

   Secondly, run the following command
   
   python process_cifar10.py //For CIFAR-10

   python process_cifar100.py //For CIFAR-100
   
   
 
 
2. Generate different noisy types and levels
    
    For example, if we want to generate 20% symmetric noisy in CIFAR-10:
    
    python generate_sn.py --noise-rate 0.2 --inDir CIFAR10/train --outDir CIFAR10/train_sn_0.2 --dataset CIFAR10

    Generate 40% asymmetric noisy in CIFAR-100

    python generate_an.py --noise-rate 0.3 --inDir CIFAR100/train --outDir CIFAR100/train_an_0.3 --dataset CIFAR100

3. After processing dataset, we can train our nested dropout network on a 20% symmetric noisy CIFAR-10 dataset by (It will save the model in the savepoints folder after finishing training):
   
   cd Train_nesteddropout

    python train_resnet.py --train-dir ../data/CIFAR10/train_sn_0.2/ --val-dir ../data/CIFAR10/val/ --dataset CIFAR10 --arch resnet18 --out-dir ./savepoints/cifar10sn0.2_model1 --nested 10 --gpu 0

4. After we have two trained model, we can use those two models as co-teaching step, to train a co-teaching model on a 20% symmetric CIFAR-10 dataset (It will save the model in co_teaching_model after training):

   cd train_coteaching
   
   python main.py --train-dir ../data/CIFAR10/train_sn_0.2/ --val-dir ../data/CIFAR10/val/ --dataset CIFAR10 --out-dir ./co_teaching_model/cifar10sn0.2_co_teaching --resumePthList ../nested/savepoints/cifar10sn0.2_model1 ../nested/savepoints/cifar10sn0.2_model2 --nested 10 --gpu 0

5. For the test step, you can use following command:

     1. Test nested dropout network
    
        cd Train_nesteddropout

        python test.py --test-dir ../data/CIFAR10/test/ --dataset CIFAR10 --arch resnet18 --resumePthList ./savepoints/cifar10sn0.2_model1 --KList 9 --gpu 0


     2. Test co-teaching network

        cd train_coteaching
        
        python test.py --test-dir ../data/CIFAR10/test/ --dataset CIFAR10 --arch resnet18 --resumePthList ./co_teaching_model/cifar10sn0.2_model1 --KList 11 --gpu 0
  
6. We also provide some trained model,for convenience, you can download them in this Google drive link:

     1. Nested dropout:
         https://drive.google.com/drive/folders/1mKESCy67mwMUlQSPL1OkASj1xg2WHeNx?usp=sharing
         
     2. Co-teaching:
         https://drive.google.com/drive/folders/11ckiGahY8d4jeTBiH8MgBMRoU_AQsErp?usp=sharing
   


## References
1. https://en.wikipedia.org/wiki/Arithmetic_coding#:~:text=Arithmetic%20coding%20(AC)%20is%20a,as%20in%20the%20ASCII%20code.&text=It%20represents%20the%20current%20information,range%2C%20defined%20by%20two%20numbers.


2. https://www.geeksforgeeks.org/lzw-lempel-ziv-welch-compression-technique/
3. https://en.wikipedia.org/wiki/LZ77_and_LZ78
4. https://blog.csdn.net/luoshixian099/article/details/50331883
5. The slides from the CAS722 course (by instructor Neerja Mhaskar), the lecture notes from CSE701 (by Dr. Barak Shoshany).

