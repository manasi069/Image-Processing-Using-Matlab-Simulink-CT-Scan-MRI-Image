# Image-Processing-Using-Matlab-Simulink-CT-Scan-MRI-Image
main code

close all
clear
clc
%% Read Images
 
% the size of images must be equal
 
[file, pathname] = uigetfile('*.jpg','Load Image 1 ');cd(pathname);
a=imread(file);
[Ax,Ay,Az]=size(a);
if Az>1
    a=rgb2gray(a);
end
[file, pathname] = uigetfile('*.jpg','Load Image 2 ');cd(pathname);
b=imread(file);
[Bx,By,Bz]=size(b);
if Az>1
    b=rgb2gray(b);
end
 
 
%%   Wavelet Transform 
 
 
[a1,b1,c1,d1]=dwt2(a,'db2');
[a2,b2,c2,d2]=dwt2(b,'db2');
 
[k1,k2]=size(a1);
 
 
%% Fusion Rules
 
%% Average Rule
 
for i=1:k1
    for j=1:k2
        a3(i,j)=(a1(i,j)+a2(i,j))/2;
   end
end
 
%% Max Rule
 
 
for i=1:k1
    for j=1:k2
        b3(i,j)=max(b1(i,j),b2(i,j));
        c3(i,j)=max(c1(i,j),c2(i,j));
        d3(i,j)=max(d1(i,j),d2(i,j));
    end
end
 
 
%% Inverse Wavelet Transform 
 
c=idwt2(a3,b3,c3,d3,'db2');
[Cx,Cy,Cz]=size(c);
a=imresize(a,[Cx,Cy]);
b=imresize(b,[Cx,Cy]);
imshow(a)
title('First Image')

figure,imshow(b)
title('Second Image')

figure,imshow(c,[])
title('Fused Image');

 
 [M,N]=size(a);
  [M1,N1]=size(b);
%% Performance Criteria
 
CR1=corr2(a,c);
CR2=corr2(b,c);
S1=snrr(double(a),double(c));
S2=snrr(double(b),double(c));
MSE1 = sum(sum((double(a)-double(c)).^2))/(M*N);
MSE2 = sum(sum((double(b)-double(c)).^2))/(M1*N1); 

fprintf('Correlation between first image and fused image =%f \n\n',CR1);
fprintf('Correlation between second image and fused image =%f \n\n',CR2);
fprintf('SNR between first image and fused image =%4.2f db\n\n',S1);
fprintf('SNR between second image and fused image =%4.2f db \n\n',S2);
fprintf('MSE between first image and fused image =%4.2f db \n\n',MSE1);
fprintf('MSE between second image and fused image =%4.2f db \n\n',MSE2);




