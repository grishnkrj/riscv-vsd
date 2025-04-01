# DAY3

## MUX

### Simple MUX

![Structure](image-1.png)
![Waveform](image-3.png)

The TL verilog code for this is

```tlverilog
$out = $sel ? $in1 : $in0 ;
```

### 8-bit MUX

![Structure](image.png)
![Waveform](image-2.png)

The TL verilog code for this is

```tlverilog
 $out[7:0] = $sel ? $in1[7:0] : $in0[7:0] ;
 ```

## Combinational Calculator

![Structure](image-4.png)
![Waveform](image-5.png)

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

![Structure](image-8.png)
![Waveform](image-7.png)

Code:

```tlverilog
   $num[31:0] = $reset ? 1 : (>>1$num + >>2$num);
```

### Free Running Counter

![Structure](image-9.png)
![Waveform](image-10.png)

Code:

```tlverilog
$count[31:0] = $reset ? 0 : ( 1 + >>1$count);
```

### Sequential Calculator

![Structure](image-11.png)
![Waveform](image-12.png)

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

![Structure](image-13.png)

![Waveform](image-14.png)

![Diagram](image-15.png)

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

### Fibonacci Counter

![Structure](image-16.png)

![Waveform](image-17.png)

![Diagram](image-18.png)

Code:

```tlverilog
   |fibonacci
      @0
         $op[7:0] = *reset ? 1 : (>>1$op + >>2$op);
```

## LAB : Pipeline

### Reproducing Diagram

#### Question

![Question](image-19.png)

#### Solution

![Solution](image-20.png)

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

![Structure](image-21.png)
![BlockDiagram](image-22.png)
![Waveform](image-23.png)

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

### Total Distance Calculator

![Structure](image-24.png)

![Waveform](image-26.png)

![Block Diagram](image-25.png)

makerchip: [Link](https://myth.makerchip.com/sandbox/0L9fPhjl8/0vgh7GG)

Code:

```tlverilog

\SV
   `include "sqrt32.v";

   m5_makerchip_module
\TLV
   
   
   |calc
      @0
         $reset = *reset;
      ?$valid
         @1
            $aa_sq[7:0] = $aa[3:0] * $aa[3:0];
            $bb_sq[7:0] = $bb[3:0] * $bb[3:0];
         @2
            $cc_sq[7:0] = $aa_sq + $bb_sq ;
         @3
            $cc[3:0] = sqrt($cc_sq);
   
      @4
         $total_distance[31:0] = $reset ? '0 : $valid ? ($cc + >>1$total_distance) : $RETAIN;//Same functionality
         //$total_distance = $reset ? 0 : $valid ? ($cc + >>1$total_distance) : >>1$total_distance;
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;
\SV
   endmodule

```

### 2-Cycle Calculator with Validity

![Structure](image-27.png)
![Block Diagram](image-28.png)
![Waveform](image-29.png)

MakerChip: [Link](https://myth.makerchip.com/sandbox/0L9fPhjl8/0P1hKOw)

Code:

```tlverilog
\TLV
   |calc
      @0
         $reset = *reset;
      @1
         $valid = $reset ? 0 : (1'b1 + >>1$valid);
         $valid_or_reset = ( $valid || $reset );
      ?$valid_or_reset
         @1   
            $val1[31:0] = >>2$out ;
            $val2[31:0] = $rand2[3:0] ;
            $sum[31:0] = $val1[31:0] + $val2[31:0] ;
            $prod[31:0] = $val1[31:0] * $val2[31:0] ;
            $diff[31:0] = $val1[31:0] - $val2[31:0] ;
            $quot[31:0] = $val1[31:0] / $val2[31:0] ;
         @2
            $out[31:0] = $reset ? 32'b0 : !$op[1] ? !$op[0] ? $sum[31:0] : $diff[31:0] : !$op[0] ? $prod[31:0] : $quot[31:0] ;

   // Assert these to end simulation (before Makerchip cycle limit).
   *passed = *cyc_cnt > 40;
   *failed = 1'b0;

\SV
   endmodule

```



