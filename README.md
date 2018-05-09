# pytorch_convolutional_rnn

The pytorch implemenation for convolutional rnn is alreaedy exisitng other than my module, for example.

- https://github.com/ndrplz/ConvLSTM_pytorch
- https://github.com/jacobkimmel/pytorch_convgru

However, there are no modules supporting neither variable length tensor nor bidirectional rnn.

I implemented ``AutogradConvRNN`` by referring to ``AutogradRNN`` at https://github.com/pytorch/pytorch/blob/master/torch/nn/_functions/rnn.py
, so my convolutional RNN module has similar structure to the orignal RNN module and supports the above features.

## Require
- python3 (Not supporting python2 because I prefer type annotation)
- pytorch>=0.4

## Feature
- Implemented at python level, without any additional CUDA kernel, c++ cocds.
- Convolutional RNN, Convolutional LSTM, Convolutional Peephole LSTM, Convolutional GRU
- Unidirectional, Bidirectional
- 1d, 2d, 3d
- Supporting PackedSequence (Supporting variable length tensor)
- Supporting nlayers RNN and RNN Cell, both.
- Not supporting different hidden sizes for each layers (But, it is very easy to implement it by stacking 1-layer-CRNNs)

## Example
```python
import torch
import convolutional_rnn
from torch.nn.utils.rnn import pack_padded_sequence

net = convolutional_rnn.Conv3dGRU(in_channels=2,  # Correspond to input size
                                  out_channels=5,  # Correspond to hidden size
                                  kernel_size=(3, 4, 6),  # Int or List[int]
                                  num_layers=3,
                                  bidirectional=True,
                                  dilation=2, stride=2, dropout=0.5)
x = pack_padded_sequence(torch.randn(3, 2, 2, 10, 14, 18), [3, 1])
print(net)
y, h = net(x)
print(y.data.shape)


cell = convolutional_rnn.Conv2dLSTMCell(in_channels=3, out_channels=5, kernel_size=3).cuda()
time = 6
input = torch.randn(time, 16, 3, 10, 10).cuda()
output = []
for i in range(time):
    if i == 0:
        hx, cx = cell(input[i])
    else:
        hx, cx = cell(input[i], (hx, cx))
    output.append(hx)

```
