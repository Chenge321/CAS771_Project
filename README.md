

<h1 align="center">CAS771 Project</h1>

In this project, we combine the nested dropout method and co-teaching method to deal with noisy label datasets.

- [How to use it?](#how-to-use-it)
- [References](#references)

## How to use it?
Note: We use a Windows machine to implement this method, the OS command is different in Macos or other system, so it may causes error.

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
   
   1. For training CIFAR-10/CIFAR-100
   
      cd Train_nesteddropout

      python train_resnet.py --train-dir ../data/CIFAR10/train_sn_0.2/ --val-dir ../data/CIFAR10/val/ --dataset CIFAR10 --arch resnet18 --out-dir ./savepoints/cifar10sn0.2_model1 --nested 10 --gpu 0
    2. For training ANIMAL-10N
        
        cd Train_nesteddropout

        python train_vgg.py --train-dir ../data/Animal10N/train/ --val-dir ../data/Animal10N/test/ --dataset Animal10N --arch vgg19-bn --out-dir ./savepoints/Animal10N_model1 --gpu 0



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
1. Han, B., Yao, Q., Yu, X., Niu, G., Xu, M., Hu, W., ... & Sugiyama, M. (2018). Co-teaching: Robust training of deep neural networks with extremely noisy labels. Advances in neural information processing systems, 31.

2. Rippel, O., Gelbart, M., & Adams, R. (2014, June). Learning ordered representations with nested dropout. In International Conference on Machine Learning (pp. 1746-1754). PMLR.

3. Chen, Y., Shen, X., Hu, S. X., & Suykens, J. A. (2021). Boosting co-teaching with compression regularization for label noise. In Proceedings of the IEEE/CVF Conference on Computer Vision 

4. Learning_with_noisy_labels, the slides from the CAS771 course (by instructor Wenbo He), https://avenue.cllmcmaster.ca/d2l/le/content/457287/viewContent/3594129/View?ou=457287 

