# DL-Fall25 Run Log - <Wensheng Yu>

## Config
model: Llama3-8B (unsloth, 4bit)
lora_r: _32__
lora_alpha: _64__
lora_dropout: _0.05__
max_seq_length: _2048__
num_train_epochs: _2__
learning_rate: _1e-4__
batch_size x grad_accum: _4__ x _4__
scheduler/warmup ratio: _cosine__ / _0.1__
seed: 42  (fixed)

## Data split
train size: _15000__
val size: _1000__
stratify: no

## Inference
do_sample: False
max_new_tokens: 8
parser: baseline

## Results
val_acc: _0.7130__
kaggle_public: _0.81371__ (filled by the submitter)
notes: (problems occurred/trainning time)

checkpoint2:
Increasing max_steps from 50 to 200, r from 1 to 16, and number of training loops can significantly increasing accuracy from baseline (0.68 to 0.73 on validation set from index 5000-6000 with evaluation batch size = 4).
I also tested it with new validation set from index 15000-16000 with evaluation batch size = 8, the accuracy decreases to 0.64.


Learned:
Increasing r and lora_alpha makes the model to learn more, lora_dropout prevents overfitting.
lr_scheduler_type cosine helps the learning rate to converge.


checkpoint3_largedata_butbad:
After I expanded my dataset from 5000 to 15000, increased the per_device_train_batch_size from 2 to 4, increased gradient_accumulation_steps from 4 to 8, changed lora_alpha from 2 to 32, changed lora_dropout from 0 to 0.05, decreased learning rate from 2e-4 to 1e-4, increased warm-up steps from 5 to 50, and further increase max_steps from 200 to 400, the accuracy on my validation set: 0.70 on validation set with evaluation batch size = 8.

Not saved:
Then I reduce max_steps back to 200, set per_device_train_batch_size back to 2, reduce warm_up steps from 50 to 20, set learning_rate back to 2e-4, lora_dropout back to 0. Result: accuracy 0.7040 on validation set with evaluation batch size = 8.

checkpoint3_better_param_largedata:
Further increase r from 16 to 32, lora_alpha from 32 to 64, set lora_dropout to 0.05. Instead of using max_steps of 200 and warm_up steps of 20, use num_train_epochs = 2,warmup_ratio = 0. Set learning rate from 2e-4 to 1e-4 to fit large training time. Result: accuracy 0.6680 on validation set with evaluation batch size = 8.

New finding: smaller batch size in evaluation increases the accuracy of the model, batch size 8 to 4 makes accuracy from 0.67 to 0.7380.
Public score: 0.79581

Redo evaluation functions to make it batch size invariant: 0.7150 when batch size = 16; 0.7120 when batch size = 25; 0.7100 when batch size = 8.

With new evaluation function:
0.6310 for checkpoint2
0.6690 for checkpoint3_largedata_butbad
0.7120 for checkpoint3_better_param_largedata


Edit only inference prompt to """Check whether a solution to a given maths question is correct or not. Your response should be 'True' if the solution is correct, otherwise 'False'. Below is the Question and Solution.
Question:
{}
Solution:
{}
Output:
"""

accuracy on validation set increases to 0.7210.

checkpoint4:
2 epochs of original prompt, 200 steps with new precise prompt.
accuracy on validation set: 0.7130
Public score: 0.81371

checkpoint5:
full retrain for 2 epochs with new prompt
accuracy on validation set: 0.6910
Public score: 0.81170