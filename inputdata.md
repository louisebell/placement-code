clear all;
n=100000;
year=0;
data1=[];
data2=[];
data3=[];
data4=[];
b=0;
count=0;


filecoord = fopen('coord.txt','r'); %open coordinate file
fileworm = fopen('worm.txt','r'); %open individual worm burden file
fileyear = fopen('year.txt','r'); %open t data in years file

for i=1:120 %loop through number of data sets contained in file

sizeA = [2 1];
sizeB = [1 1];

while year>-1
%input data

    formatSpec = '%d';
    year = fscanf(fileyear,formatSpec,sizeB); %input one year

    if(isempty(year) || year==-1)  %if no data left in file, or at end of data set, break
        b=b+1;
        break
    end

    formatSpec = '%f';
    worm = fscanf(fileworm,formatSpec,sizeB); %input one individals worm burden

    %formatSpec='%d';   %file format y
    formatSpec = '[%d, %d]\n';  %file format [x,y]
    coord = fscanf(filecoord,formatSpec,sizeA); %input coordinate
    coord=coord';
    ycoord=coord(:,2);
    xcoord=coord(:,1);


    data1=[data1;year]; %if not at end of one simulation add data to existing data set
    data2=[data2;ycoord];
    data3=[data3;xcoord];
    data4=[data4;worm];

end

if(isempty(year)) %if no data left, finish
    b=b+1;
    break;
end

length(data1)
 if(length(data1)>n) %if simulation has run full course and not failed early, i.e we have more than n pieces of data in the data set

    %1D model
%     data2=data2/2; %convert grid squares to km (needed for sim data)
%     data3=data3/2;
    %x=ycoord; %for data in km
%     data1=data1+1; %as simulation starts 2 years after inital point
%     data4=floor(data4); %round to integer, needed for sim data due to division 

%     %sample
%     [t,idx] = datasample(data1,5000);
%    y = data2(idx);
%     x=data3(idx);
%     w= data4(idx);


    %radial model
    data2=(data2/2)-20; %convert grid squares to km (needed for sim data) and recenter to dist from middle of arena
    data3=(data3/2)-20;
    %x=ycoord; %for data in km
    data1=data1+1; %for data in years
    data4=floor(data4); %round to integer, needed for sim data due to division
    rcoord=data2.^2+data3.^2; %radial distance from centre (r^2)
    
    %sample 5000 data points at random
    [t,idx] = datasample(data1,5000);
    %x = ycoord(idx);
    w= data4(idx);
    r=rcoord(idx);

    [out(i,:),fval(i)]=maxlikhoodrad(r,t,w); %store optimised parameter values in out, loglihood in a
    
    

 end

year=0;
data1=[];
data2=[];
data3=[];
data4=[];

end
fclose(fileyear);
fclose(fileworm);
fclose(filecoord);

