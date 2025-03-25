# Spartan-6-with-7-segment
Spartan-6 FPGA Time-Multiplexed 8-Digit 7-Segment Display with Custom Sequence


module seq8bit(input clk,reset,
 output reg [3:0]counter1,counter2,
 output reg [3:0]counter1bar,counter2bar,
 output reg [7:0]display,
 output reg [3:0]control);
reg clkdiv,clkms,flag;
reg e110,e30,e118,e181,e84,e91,e40,e65,e187,e254;
integer temp1,temp2;

initial
 begin
  temp1=0;
  temp2=0;
  counter1=4'b1110; 
  counter2=4'b0110;
  clkdiv=1'b0;
  flag=0;
  clkms=0;
end

// clock divider logit
always@(posedge clk)
begin
temp1=temp1+1;
temp2=temp2+1;
if(temp1==2000000)
begin
clkdiv=~clkdiv; // clock signal for time multiplexing
temp1=0;
end
if(temp2==200)
begin
clkms=~clkms; // clock signal for counter
temp2=0;
end
end


// Sequence decide logic using dataflow style
always@(posedge clkdiv)
begin
if(reset)
begin
counter1=4'b1110;
counter2=4'b0110;
end
else
begin
counter1bar=~counter1;
counter2bar=~counter2;


e110=counter2bar[3] & counter2[2] & counter2[1] &counter2bar[0] & counter1[3] & counter1[2] & counter1[1] & counter1bar[0]; 

e30=counter2bar[3] & counter2bar[2] & counter2bar[1] &counter2[0] & counter1[3] & counter1[2] & counter1[1] & counter1bar[0]; 

e118=counter2bar[3] & counter2[2] & counter2[1] &counter2[0] & counter1bar[3] & counter1[2] & counter1[1] & counter1bar[0]; 

e181=counter2[3] & counter2bar[2] & counter2[1] &counter2[0] & counter1bar[3] & counter1[2] & counter1bar[1] & counter1[0]; 

e84=counter2bar[3] & counter2[2] & counter2bar[1] &counter2[0] & counter1bar[3] & counter1[2] & counter1bar[1] & counter1bar[0]; 

e91=counter2bar[3] & counter2[2] & counter2bar[1] & counter2[0] & counter1[3] & counter1bar[2] & counter1[1] & counter1[0]; 

e40=counter2bar[3] & counter2bar[2] & counter2[1] &counter2bar[0] & counter1[3] & counter1bar[2] & counter1bar[1] & counter1bar[0]; 

e65=counter2bar[3] & counter2[2] & counter2bar[1] &counter2bar[0] & counter1bar[3] & counter1bar[2] & counter1bar[1] & counter1[0]; 

e187=counter2[3] & counter2bar[2] & counter2[1] &counter2[0] & counter1[3] & counter1bar[2] & counter1[1] & counter1[0]; 

e254=counter2[3] & counter2[2] & counter2[1] &counter2[0] & counter1[3] & counter1[2] & counter1[1] & counter1bar[0]; 



counter2[3]= e118 | e65 | e187;

counter2[2]= e30 | e181 | e84 | e40 | e187 | e254;

counter2[1]=e30 | e118 | e91 | e65 | e187 | e254;

counter2[0]= e110 | e30 | e118 | e181 | e84 | e65 | e187;

counter1[3]=e110 | e84 | e91 | e65 | e187 | e254;

counter1[2]=e110 | e30 | e118 | e181 | e187 | e254;

counter1[1]=e110 | e30 | e84 | e65 | e187 | e254;

counter1[0]=e118 | e84 | e40 | e65 ;

end
end

always@(posedge clkms)
begin
 if (flag==1'b0)
 begin
  control=4'b0111; 

  case(counter2)   // hex - sevensegment decoder for seg 1
   0:display=8'b11111100;
   1:display=8'b01100000;
   2:display=8'b11011010;
   3:display=8'b11110010;
   4:display=8'b01100110;
   5:display=8'b10110110;
   6:display=8'b10111110;
   7:display=8'b11100000;
   8:display=8'b11111110;
   9:display=8'b11110110;
   10:display=8'b11101110;
   11:display=8'b00111110;
   12:display=8'b10011100;
   13:display=8'b01111010;
   14:display=8'b10011110;
   15:display=8'b10001110;
  endcase
  flag=1'b1;
 end
else
 begin
control=4'b1011;  
case(counter1)   // hex - sevensegment decoder for seg 2
0:display=8'b11111100;
1:display=8'b01100000;
2:display=8'b11011010;
3:display=8'b11110010;
4:display=8'b01100110;
5:display=8'b10110110;
6:display=8'b10111110;
7:display=8'b11100000;
8:display=8'b11111110;
9:display=8'b11110110;
10:display=8'b11101110;
11:display=8'b00111110;
12:display=8'b10011100;
13:display=8'b01111010;
14:display=8'b10011110;
15:display8'b10001110;
endcase
flag=1'b0;
end
end

endmodule
