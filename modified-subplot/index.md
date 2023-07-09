# Modified-subplot


## Modified subplot.m

This function can return the positions of subplot, so we can easily use `axes` or `polaraxes` to plot the subpannel~
```matlab
function [p] = subposition(m,n,margin,gap)
% Uniform m*n subplot positions
% Input:
%       m,n     m*n grids
%       margin  [mL,mR,mU,mB]     
%       gap     [gW,gH]
%       
% Output:
%       p       m*n cells, the position of each grid
% 
    mL = margin(1);
    mR = margin(2);
    mU = margin(3);
    mB = margin(4);
    gW = gap(1);
    gH = gap(2);
    p = cell(m,n);
    W = (1-mL-mR-gW*(n-1))/n;
    H = (1-mU-mB-gH*(m-1))/m;
    if W<=0 | H<=0
        error('Input error!')
    end
    for im = 1:m
        for in = 1:n
            pL = mL+(W+gW)*(in-1);
            pB = mU+(H+gH)*(m-im);
            p(im,in) = {[pL,pB,W,H]};
        end
    end
end
```
<!--more-->

## For example:

```matlab
% Build a figure
figure
% set the paper
set(gcf,'unit','centimeters','position',[1,1,15,15])
% subplot num.
m = 2;  n = 2;
% margin control
margin = [0.1,0.01,0.1,0.1];
% gap control
gap = [0.1,0.1];
p = subposition(m,n,margin,gap);
% main title
title('Test')
axis off
% subplot 1
axes('Position',cell2mat(p(1,1)))
surf(magic(4))
xlabel('x')
ylabel('y')
zlabel('z')
title('t1')
% subplot 2
polaraxes('Position',cell2mat(p(1,2)))
polarplot(1:10)
title('t2')
% subplot 3
axes('Position',cell2mat(p(2,1)))
scatter([1,2,3],[1,2,3])
xlabel('x')
ylabel('y')
title('t3')
% subplot 4
axes('Position',cell2mat(p(2,2)))
imagesc(randn(10))
xlabel('x')
ylabel('y')
title('t4')
```

## Result:
![subposition](/subposition.png)
