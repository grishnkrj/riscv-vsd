# DAY3

## MUX

### Simple MUX

![Structure](images/image-1.png)
![Waveform](images/image-3.png)

The TL verilog code for this is

```tlverilog
$out = $sel ? $in1 : $in0 ;
```

### 8-bit MUX

![Structure](image.png)
![Waveform](images/image-2.png)

The TL verilog code for this is

```tlverilog
 $out[7:0] = $sel ? $in1[7:0] : $in0[7:0] ;
 ```

## Combinational Calculator

![Structure](images/image-4.png)
![Waveform](images/image-5.png)

The TL verilog code for this is

```tlverilog
   $val1[31:0] = $rand1[2:0] ;
   $val2[31:0] = $rand2[2:0] ;
   
   $sum[31:0] = $val1[31:0] + $val2[31:0] ;
   $prod[31:0] = $val1[31:0] * $val2[31:0] ;
   $diff[31:0] = $val1[31:0] - $val2[31:0] ;
   $quot[31:0] = $val1[31:0] / $val2[31:0] ;
   
   $out[31:0] = $reset ? 0 : !$op[1] ? !$op[0] ? $sum[31:0] : $diff[31:0] : !$op[0] ? $prod[31:0] : $quot[31:0] ;

```

MakerIDE Project of this [Calculator](https://myth.makerchip.com/sandbox/0zpfRhoN2/0qjh8nJ)


## Sequential Logics

### Fibonacci Series

![Structure](images/image-8.png)
![Waveform](images/image-7.png)

Code:

```tlverilog
   $num[31:0] = $reset ? 1 : (>>1$num + >>2$num);
```

### Free Running Counter

![Structure](images/image-9.png)
![Waveform](images/image-10.png)

Code:

```tlverilog
$count[31:0] = $reset ? 0 : ( 1 + >>1$count);
```

### Sequential Calculator

![Structure](images/image-11.png)
![Waveform](images/image-12.png)

Code:

```tlverilog
   $val1[31:0] = >>1$out ;
   $val2[31:0] = $rand2[2:0] ;
   
   $sum[31:0] = $val1[31:0] + $val2[31:0] ;
   $prod[31:0] = $val1[31:0] * $val2[31:0] ;
   $diff[31:0] = $val1[31:0] - $val2[31:0] ;
   $quot[31:0] = $val1[31:0] / $val2[31:0] ;
   
   $out[31:0] = $reset ? 32'b0 : !$op[1] ? !$op[0] ? $sum[31:0] : $diff[31:0] : !$op[0] ? $prod[31:0] : $quot[31:0] ;
```

## Retiming

### Pythagoras Calculator

![Structure](images/image-13.png)

![Waveform](images/image-14.png)

![Diagram](images/image-15.png)

Code:

```tlverilog
   |calc

      @0
         $aa_sq[7:0] = $aa[3:0] * $aa[3:0];
         $bb_sq[7:0] = $bb[3:0] * $bb[3:0];
      @1
         $cc_sq[7:0] = $aa_sq + $bb_sq ;
      @2
         $cc[3:0] = sqrt($cc_sq);
```

### Fibonacci 


![Structure](images/image-16.png)

![Waveform](images/image-17.png)

![Diagram](images/image-18.png)

Code:

```tlverilog
   |fibonacci
      @0
         $op[7:0] = *reset ? 1 : (>>1$op + >>2$op);
```


## LAB : Pipeline

### Reproducing Diagram

#### Question

![Question](images/image-19.png)

#### Solution

![Solution](images/image-20.png)

Code:

```tlverilog

   |comp
      @1
         $error1 = $bad_input || $illegal_operation;
      @3
         $error2 = $error1 || $overflow;
      @6
         $error3 = $error2 || $division_by_zero;

```


### 2-Cycle Counter

![Structure](images/image-21.png)
![BlockDiagram](images/image-22.png)
![Waveform](images/image-23.png)

MakerChip [Link](https://myth.makerchip.com/sandbox/0zpfRhoN2/0zmhMv6#)

Code:

```tlveriilog
\TLV
   |calc
      @0
         $reset = *reset;

      @1   
         $val1[31:0] = >>2$out ;
         $val2[31:0] = $rand2[2:0] ;
         
         $sum[31:0] = $val1[31:0] + $val2[31:0] ;
         $prod[31:0] = $val1[31:0] * $val2[31:0] ;
         $diff[31:0] = $val1[31:0] - $val2[31:0] ;
         $quot[31:0] = $val1[31:0] / $val2[31:0] ;
         $cnt = $reset ? 0 : (1'b1 + >>1$cnt);
      
      @2
         $valid = $cnt;
         $reset_line = $reset || ~$valid;
         $out[31:0] = $reset_line ? 32'b0 : !$op[1] ? !$op[0] ? $sum[31:0] : $diff[31:0] : !$op[0] ? $prod[31:0] : $quot[31:0] ;

   
   // Assert these to end simulation (before Makerchip cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;

```
