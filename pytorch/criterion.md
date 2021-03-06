### loss pack ``` #pack_loss #loss #loss_pack ```
~~~
def get_loss(self, predict, target, length):
      target = pack(target, length, batch_first=True)[0]
      predict = pack(predict, length, batch_first=True)[0]
      loss = self.criterion(predict, target)
      return loss
~~~

### binary cross entropy
~~~
self.criterion=torch.nn.functional.binary_cross_entropy
predict = torch.sigmoid(x)
label = Variable(x).float() # FloatTensor
self.criterion(predict, label)
~~~

### cross entropy ``` #cross entropy with logits ```
~~~
self.criterion = torch.nn.functional.cross_entroy
predict = torch.softmax(x, dim=1)
label = Variable(x).long() # LongTensor
self.criterion(predict, label)
~~~

### kl divergence
~~~
self.criterion = torch.nn.functional.kl_div
predict = torch.nn.functional.log_softmax(x, dim=1)
target = torch.nn.functional.softmax(x, dim=1)
self.criterion(predict, target)
~~~

### nll_loss ``` #cross entropy with softmax ```
~~~ 
self.criterion = torch.nn.functional.nll_loss
predict = torch.nn.functional.log_softmax(x, dim=1)
target = torch.LongTensor(y)
self.criterion(predict, target)
~~~

#### search key
~~~
#loss #loss_function #cross_entroy #binary_cross_entropy #bce
#kld #kld_loss
~~~
