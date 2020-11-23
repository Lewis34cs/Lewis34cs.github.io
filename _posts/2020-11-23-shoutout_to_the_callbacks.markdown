---
layout: post
title:      "Shoutout to the Callbacks"
date:       2020-11-23 18:04:39 -0500
permalink:  shoutout_to_the_callbacks
---

`from tensorflow.keras.callbacks import EarlyStopping, ModelCheckpoint, ReduceLROnPlateau`

Within the fit() method, there is an argument named 'callbacks'. What this parameter does is it allows us to pass through a list of callbacks we'd like to implement on our model. There are various callbacks we can choose from, but for this blog we will focus on: Earlystopping, ModelCheckpoint, and ReduceLROnPlateau.

## **EarlyStopping:**

This callback will interrupt your model's training when it identifies that there has been no progress on whatever metric we want to monitor on the validation set for a certain number of epochs. It's great if we are training a large amount of data and don't want to overfit or waste resources. The metric we want to measure if we need to stop early is defined by the **'monitor'** argument, and the number of epochs is defined within the 'patience' argument. We can also choose to restore our model's best weights by setting **restore_best_weights** = True. We need to set the **'mode'** argument based on which metric we are montoring in terms of improvement. For example, if we were to select 'val_accuracy' as the monitor, we would want to select mode = 'max'. If we wanted to monitor 'val_loss', we would want our mode = 'min'. There is also an 'auto' option, which will determine the monitored metric and either select 'max' or 'min' based on the metric.  The **'min_delta'** argument tells the callback what is the minimum amount of change we wish to see in our monitored metric for us to consider as an improvement. Here's a simple example of everything we've explained so far:

```
  earlystop = EarlyStopping(monitor='val_loss', min_delta=0.01, patience=5, 
                           mode='min', restore_best_weights=True)
```

So we are selecting 'val_loss' to be monitored, which means our mode = 'min'. The minimum amount of change we need to see is a decrease of 0.01within 5 epochs, otherwise the earlystopping will stop the model's training. Also if there is no improvement in the model after each epoch, the model will revert it's weights back to the best trained epoch. As long as our model continues to see an improvement in decreasing its 'val_loss' by 0.01 within 5 epochs, our training will continue to run. 

With this, the EarlyStopping callback allows us to save time and resources with creating a model. But now, what if we want to save our best models that we've trained? ModelCheckpoint allows us to do just that.

## **ModelCheckpoint:**

This callback will save checkpoints of our model at regular intervals during training - this option can be changed, but the default interval is at the end of every epoch. Just like EarlyStopping, ModelCheckpoint has similar arguments including monitor and mode. These perform the exact same way as defined with EarlyStopping. New arguments within ModelCheckpoint include: filepath, save_best_only, save_weights_only, and save_freq.  for the **'filepath'** arg, you must define where you wish to save your models via a filepath, along with naming each model individually so they don't get overwritten (unless you only want to keep the best model defined by your monitored metric). The **'save_best_only'** argument defines whether we save the model only if we see an improvement in the monitored metric, or if we save all models at each interval regardless of improvement. **'save_weights_only'** by default is False, and determines whether or not we want to only save the weights of oour best models, our the entire models themselves. **'save_freq'** by default saves after the end of every epoch, if the model has improved in our monitored metric (if save_best_only=True). Here's an example:

```
  f_path = '../drive/models/'
  checkpoint_f_path = f_path+"wgt-improv-{epoch:02d}-{"+monitor+":.2f}.hdf5"
	
  checkpoint = ModelCheckpoint(filepath=checkpoint_f_path, monitor='val_loss', 
                               save_best_only=True, mode='min', 
                               save_freq='epoch')
```

Above we have specified where we want to save the models based on our checkpoint_f_path variable, along with the epoch and val_loss score inside the name of the checkpoint_f_path. We want to monitor 'val_loss' as a determining factor for model improvement. Mode must be either 'auto' or 'min' since we are using 'val_loss' as our monitored metric. We only want to save the best models, which means that we will only save models that show improvement Our save frequency is the default 'epoch', which allows us to save our model at the end of every epoch *if that model has shown improvement.* 

With ModelCheckpoint, we are able to save our best models each time we train a model - it's convenient and easy to use! 

## ReduceLROnPlateau:

This callback uses Learning Rate Decay through performance scheduling methods. What this means is that if the model's monitored metric does not show improvement after a certain number of epochs, this callback will kick in and decay our learning rate so that our model may attempt to reach the optimum values rather than continue to bounce around if our optimizer's learning rate is set too high. Here are some of ReduceLROnPlateau's arguments that we will touch on: monitor, mode, min_delta, patience, min_lr, factor, cooldown, and verbose. Just like the other callbacks, monitor, mode, min_delta, and patience are basically the same. The only difference between ReduceLROnPlateau's **'patience'** is that when it's patience is reached, the learning rate decay begins. Learning rate decay depends on the argument **'factor'** - this is the amount by which the learning rate is multiplied and ultimately reduced by. if our learning rate was set to 0.01 and we had our factor = 0.9, our new learning rate would be 0.01 * 0.9 = 0.009. **'min_lr'** gives our learning rate a metaphorical bedrock; it cannot be smaller than this number. So no matter how many times our patience intiates learning rate decay, it cannot be smaller than our 'min_lr'. The argument **'cooldown'** tells the callback how many epochs to wait until it begins counting towards the next learning rate decay. If our cooldown = 2, then the callback must wati 2 epochs until it begins it's patience counting for another learning rate decay. 
As for the argument **'verbose'**, all three of these callbacks actually have this argument as well - I just prefer to use it to see learning rate decay. Here's an example:

```
 reducelr = ReduceLROnPlateau(monitor='val_loss', mode='min', min_delta=0.01, 
                                                                  patience=3, min_lr=1e-5,factor=0.9, 
                                                                  cooldown=1, verbose=1)
```

In the above example, we are again monitoring 'val_loss', where our mode must be either 'min' or 'auto', and have designated our minimum threshold of val_loss change to be considered an improvement as 0.01. Our learning rate decay patience is set to 3, so if there is no improvement after 3 epochs, our current learning rate will be multiplied by our factor variable and this product will become the new learning rate. We've set our cooldown equal to 1, which means that once the callback initiates a learning rate decay, it must wait another epoch before it can begin monitoring for loss for 3 epochs to change the learning rate again. We've also included verbose = 1, which will tell us when the learning rate changes, and what the new learning rate is when training our model. 

These three callbacks I've found to be immensely helpful, and hope that someone finds some use out of me typing all this up. And yes, we can make a function that returns all three of these callbacks into a nice neat package:

```
def three_callbacks(monitor='val_loss', min_delta=0, patience=0, mode='auto', 
                   f_path='../drive/models/', restore_best_weights=False, 
									 save_best_only=False, save_freq='epoch', lr_patience=5, factor=0.1, 
									 cooldown=0):
  """
  Definition:
  Creates three variables, and then returns these variables in a list.

  1. variable 'earlystop' stops model training when the 'monitor' metric has not 
  improved past the 'min_delta' threshold after a certain amount of epochs, 
  defined by 'patience'. 

  2. variable 'checkpoint' saves model at some interval, so the model can be 
  loaded later on to continue the training from the saved state.
	
	3. variable 'reducelr' reduces learning rate after a certain number of intervals.

  Args:
  monitor: default = 'val_loss'. Quantity to be monitored during training.
  min_delta: default = 0. minimum change in the monitored quantity to be
  considered an improvement.
  patience: default = 0. The number of epochs with no improvement after which
  training will be stopped.
  mode: default = 'auto'. Defines the direction of monitor quantity. ex. If 'min', 
  training will stop after monitored quantity is no longer decreasing past the 
  defined min_delta. 
  f_path: default = '../drive/models/'.  
  The filepath that will be created / set as the destination for saved models 
  from 'checkpoint' variable.
  restore_best_weights: default = False. Defines whether or not to restore the 
  best model weights from the epoch with the best value of the monitored quantity.
  save_best_only: default = False. If save_best_only=True, the latest best model 
  according to the quantity monitored will not be overwritten.
  save_freq: default = 'epoch'. The defined interval at which the model is saved.
  lr_patience: default = 5. Dictates how long the ReduceLROnPlateau callback must 
	wait until it can initiate learning rate decay if there is no improvement in the monitored
	metric.
	factor: default = 0.1. Float that is multipled by the current learning rate. This action is 
	learning rate decay, and the product is the new learning rate until training stops.
	cooldown: default = 0. Defines how long the ReduceLROnPlateau must wait until it
	may begin lr_patience after initiating learning rate decay. 

  Returns:
  A list named 'callbacks' containing the 'earlystop', 'checkpoint', and
  'reducelr' variable.
  """
  
  # Defining our early stopping func
  earlystop = EarlyStopping(monitor=monitor, min_delta=min_delta, patience=patience, 
                           mode=mode, restore_best_weights=restore_best_weights)
    
  #checking to make sure our path is set up. If not, the line below creates it.
  os.makedirs(f_path, exist_ok=True)
    
  # saves the current model at the specified checkpoint
  checkpoint_f_path = f_path+"wgt-improv-{epoch:02d}-{"+monitor+":.2f}.hdf5"
  checkpoint = ModelCheckpoint(filepath=checkpoint_f_path, monitor=monitor, 
                               save_best_only=save_best_only, mode=mode, 
                               save_freq=save_freq)
  reducelr = ReduceLROnPlateau(monitor=monitor, mode=mode, min_delta=min_delta, 
                               patience=lr_patience, min_lr=1e-5,factor=factor, 
                               cooldown=cd, verbose=1)
  callbacks = [earlystop, checkpoint, reducelr]
  return callbacks
```
