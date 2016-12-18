---
layout: post
title: "Tonemapping for HDR Images in Matlab"
date: 2016-11-12
catergories: Projects, Electrical, Signals
comments: true
---
In this project I detail the process of preparing and tonemapping a HDR image for viewing via Matlab.

## Opening HDR Images in Matlab

The command to open HDR files on Matlab is <a href="https://www.mathworks.com/help/images/ref/hdrread.html">`hdrread()`.</a> What you will receive is a [x,y,3] matrix for an image of (x,y) resolution. The three matrices in depth hold the RGB information separately. Since we will have to reuse the information, it's best that the image file is stored in a handle like such:

`IMG = hdread('img.hdr');`  

## Extraciting Luminance

Since tonemapping is applied on luminance, it would make sense to extract the luminance of our image. To do that we will use the relative [luminance equation](https://en.wikipedia.org/wiki/Relative_luminance).

`lum = 0.213*IMG(:,:,1)+0.715*IMG(:,:,2)+0.072*IMG(:,:,3);`  
`x = log10(lum)`

Very important here that `log10` is used instead of `log`, as the latter computes the natural log that will give you problems.


## Performing Histogram Equalization

The problem of performing histogram equalization on Matlab is that the native image equalization function on Matlab will not work, thus the range of luminace that are dealt with are different. Thus it was necessary to perform manual histogram equalization.

{% highlight m linenos%}
x = x(:)-min(x(:));
[n,edge]=histcounts(x,index1);
nk = [0 n];
rk = edge/(max(edge)-min(edge))*index0; %bins
pr = nk/sum(nk); %density
cdf = cumsum(pr)/sum(pr);
s = round(max(rk)*cdf,0);
fin = zeros(size(rk)); %equalized histogram luminance values
for i = 1:index0
fin(s(i)+1) = fin(s(i)+1)+pr(i);
end
fincdf = cumsum(fin)/sum(fin);
new_lum = zeros(1080,1920);
lum_index = round(lum,0)+1;
for i = 1:1080
   for y = 1:1920
       new_lum(i,y) = fincdf(lum_index(i,y));
   end
end
{% endhighlight %}

Where:  
•	Wmax = max log luminance value   
•	Wmin = min log luminance value   
•	Lum = log luminance value scaled between [1,250] so can be used as index   
•	nk = density   
•	rk = bins   
•	pr = percentage density   
•	cdf = cumulative sum of percentage density of old histogram   
•	s = converting bins to index values   
• fin = final equalized histogram   

## Applying Linear Ceiling

The next step is to apply a ceiling. The original purpose of this project was to examine the difference between linear ceilings and perceptual ceilings. First this is my approach to a linear ceiling:

{% highlight m lineos %}
fin = fin * 1920 * 1080; %convert percentage to actual number
tolerance = 2.5/100*sum(fin);
range = wmax:(wmax-wmin)/index2:wmin;
trimmings = tolerance+1;
lw = log10(extract_lum(hdr_img));
world_lum = 0:(max(lw(:))-min(lw(:)))/index2:max(lw(:))-min(lw(:));
pbw_max = 1.5;
pbw_min = 0;
pbw = pbw_min:(pbw_max-pbw_min)/index2:pbw_max;
while trimmings > tolerance
trimmings = 0;
T = sum(fin); %calculate total sample points
    if T > tolerance
        for i = 1:index2
             ceiling = 1/250*T
            if fin(i) > ceiling     
                trimmings = trimmings + fin(i) - ceiling; %trim update
                fin(i) = ceiling; %reduce ceiling
            end
        end
    else
        continue;
    end
end
{% endhighlight %}


## Perceptual Ceiling

Below is my approach to achieving a perceptual ceiling based on Larson et al. (2011)

{% highlight m lineos %}
fin = fin * 1920 * 1080; %convert percentage to actual number
tolerance = 2.5/100*sum(fin);
range = wmax:(wmax-wmin)/index2:wmin;
trimmings = tolerance+1;
lw = log10(extract_lum(hdr_img));
world_lum = 0:(max(lw(:))-min(lw(:)))/index2:max(lw(:))-min(lw(:));
pbw_max = 1.5;
pbw_min = 0;
pbw = pbw_min:(pbw_max-pbw_min)/index2:pbw_max;
while trimmings > tolerance
trimmings = 0;
T = sum(fin); %calculate total sample points
    if T > tolerance
        for i = 1:index2
             Bde = 2*pbw(i);
             ld = (Bde)^10;
             lw = world_lum(i)^10;
             ld_p = perceptual_ceiling(log10(ld));
             lw_p = perceptual_ceiling(log10(lw));
             ceiling = 1/index0*T*lw/ld*ld_p/lw_p;
            if fin(i) > ceiling     
                trimmings = trimmings + fin(i) - ceiling; %trim update
                fin(i) = ceiling; %reduce ceiling
            end
        end
    else
        continue;
    end
end
{% endhighlight %}

Here, the pbw max and min values are the target display range in log10 scale with 252 levels corresponding to the number of bins I used. And the look up table used in the code is:

{% highlight m lineos %}
function ceiling = perceptual_ceiling(luminance)
             switch luminance
                 case luminance < -3.94
                     ceiling = -2.86;
                 case luminance >= -3.94 && luminance <-1.44
                     ceiling  = (0.405*luminance+1.6)^2.18-2.86;
                 case luminance >= -1.44 && luminance < -0.0184
                     ceiling = luminance - 0.395;
                 case luminance >= -0.0184 && luminance < 1.9
                     ceiling = (0.249*luminance+0.65)^2.7-0.72;
                 otherwise
                     ceiling = luminance - 1.255;
              end
end
{% endhighlight %}


## Conversion back into luminance Space and Normalization

Now that the image has been modified, the image needs to be processed for display. To do so, each pixel is interpolated using the histogram modified in the previous section, then together as a matrix converted out of log space and normalized.

{% highlight m lineos %}
fincdf = cumsum(fin)/sum(fin);
new_lum = zeros(1080,1920);
lum_index = round(lum,0)+1;
for i = 1:1080
   for y = 1:1920
       new_lum(i,y) = fincdf(lum_index(i,y));
   end
end

new_lum = (new_lum).^10;
new_lum = (new_lum-(min(new_lum(:))))/(max(new_lum(:))-min(new_lum(:)));
{% endhighlight %}

## Replacing Old Luminance Values

{% highlight m lineos %}
lum = (extract_lum(hdr_img)); %step3
    for i = 1:1080
        for y = 1:1920
           if lum(i,y) < 1
               lum(i,y) = 1;
           end
        end
    end
ims = zeros(1080,1920,3);
figure;
imshow(new_lum);
ims(:,:,1) = (hdr_img(:,:,1)./lum).*(new_lum);
ims(:,:,2) = (hdr_img(:,:,2)./lum).*(new_lum);
ims(:,:,3) = (hdr_img(:,:,3)./lum).*(new_lum);
{% endhighlight %}


## Clipping and Gamma Correction

{% highlight m lineos %}
for k = 1:3
    frame = ims(:,:,k);
    for i = 1:1080
        for y = 1:1920
           if frame(i,y) > 1
               frame(i,y) = 1;
           end
        end
    end
    ims(:,:,k) = frame.^(1/2.2);
end
figure
imshow(ims);
{% endhighlight %}


## Results

![]({{site.urlt}}/img/2016-12-17-1.png)
![]({{site.urlt}}/img/2016-12-17-2.png)
![]({{site.urlt}}/img/2016-12-17-3.png)
![]({{site.urlt}}/img/2016-12-17-4.png)
![]({{site.urlt}}/img/2016-12-17-5.png)
![]({{site.urlt}}/img/2016-12-17-6.png)
![]({{site.urlt}}/img/2016-12-17-7.png)

Hopefully when I have more time, I can upload pictures of better quality including the histogram changes through the process as well

---
Project from assignment of partial fulfillment of EECE 421, December 2016.
