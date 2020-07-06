Functions: 
bitAnd, bitXor, getByte, logicalShift, bitCount, bang, negate, tmin, isTmax, fitsBits, divpwr2, isPositive, isLessOrEqual, ilog2, float_neg

```c
and: & *
or: | +
not: ~ -
/* 
 * bitAnd - x&y using only ~ and | 
 *   Example: bitAnd(6, 5) = 4
 *   Legal ops: ~ |
 *   Max ops: 8
 *   Rating: 1
 */
int bitAnd(int x, int y) {
// De Morgan's laws: ~(p&q) = ~p|~q, ~(p|q) = ~p&~q
/* x & y = ~(~(x&y)) = ~(~x|~y) */
  return ~(~x|~y);
}

/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
// De Morgan's laws
// a xor b = (~a | ~b) & (a | b)
//         = ~(a & b) & ~(~a & ~b)
  return ~(x&y)&~(~x&~y);
}

/* 
 * getByte - Extract byte n from word x
 *   Bytes numbered from 0 (LSB) to 3 (MSB)
 *   Examples: getByte(0x12345678,1) = 0x56
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 6
 *   Rating: 2
 */
int getByte(int x, int n) {
  /* right shift in order to avoid boring arithmatic shift and try to get specific byte using bitAnd and mask. */
  // n<<3: expand into the bit wide. 1 Byte = 8 bits, thus need 3 here. 
  // x>>(n<<3): push the int right forward.
  // & 0xFF: use the mask to obtain the lowest byte. 
  // Power-of-2 Multiply with shift: u << k == u * 2^k
  // Power-of-2 Divide with shift: u >> k == u / 2^k
  return x>>(n<<3) & 0xFF;
}

/* 
 * logicalShift - shift x to the right by n, using a logical shift
 *   Can assume that 0 <= n <= 31
 *   Examples: logicalShift(0x87654321,4) = 0x08765432
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 20
 *   Rating: 3 
 */
int logicalShift(int x, int n) {
  int mask = ((1 << 31) >> n) << 1; // f0000000
  // printf("%x\n", ~mask);
  return (x >> n) & (~mask);
}

/*
 * bitCount - returns count of number of 1's in word
 *   Examples: bitCount(5) = 2, bitCount(7) = 3
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 40
 *   Rating: 4
 */
int bitCount(int x) {
  // int mask = 0x55 + (0x55 << 8) + (0x55 << 16) + (0x55 << 24);
  // x = (x & mask) + ((x >> 1) & mask);
  // mask = 0x33 + (0x33 << 8) + (0x33 << 16) + (0x33 << 24);
  // x = (x & mask) + ((x >> 2) & mask);
  // mask = 0xF + (0xF << 8) + (0xF << 16) + (0xF << 24);
  // x = (x & mask) + ((x >> 4) & mask);
  // return (x + (x >> 8) + (x >> 16) + (x >> 24)) & 0xFF;

  int mask1, mask2, mask4, mask8, mask16;
  mask1 = 0x55 | 0x55 << 8; 
  mask1 = mask1 | mask1 << 16; 
  mask2 = 0x33 | 0x33 << 8; 
  mask2 = mask2 | mask2 << 16; 
  mask4 = 0x0f | 0x0f << 8; 
  mask4 = mask4 | mask4 << 16; 
  mask8 = 0xff | 0xff << 16; 
  mask16 = 0xff | 0xff << 8;
  x = (x & mask1) + ((x >> 1) & mask1); 
  x = (x & mask2) + ((x >> 2) & mask2); 
  x = (x + (x >> 4)) & mask4; 
  x = (x + (x >> 8)) & mask8; 
  x = (x + (x >> 16)) & mask16;
  return x;
}

/* 
 * bang - Compute !x without using !
 *   Examples: bang(3) = 0, bang(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int bang(int x) {
  /* for x != 0, the highest bit of x|(-x) will always be 1; when x == 0, the result is the opposite */
  return ~(((~x+1)|x)>>31)&1;
  }
  
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return ~x+1;
}

/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  // output -2*(w-1). w = 32 for integer. 0x80000000
  return 0x1<<31;
}

/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
// creates i = x plus 1 then makes x = that number plus x.
// then flips the bits of x and bangs i, adds them and returns.
// this effectively checks if the number was tmax
// because if it was adding 1 would result in a leading 1.
// you flips the bits to get 1 and add either 0 or 1
// and return the opposite of that by banging x to get 1 for true or 0 for flase
  int i = x + 1;
  x = x + i;
  x = ~x;
  i = !i;
  x = x + i;
  return !x;
}

int fitsBits(int x, int n) {
/* if fitsBits then from highest bit to n bit will all become 1 - negative number or 0 - positive number
 * then can construct a mask to implement fitsBits with the help of ^ and !
 */
  // return !((x >> (n + (~1) + 1)) ^ (((1 << 31) & x) >> 31));

  int shiftnum = 32 + (~n + 1); //the number of bits shifted, 32-n
  int shiftleft = x << shiftnum; 
  int shiftleftandright = shiftleft >> shiftnum;
  int result = !(x ^ shiftleftandright); //if the number after shift is same as ever
  return result;
}

/* 
 * divpwr2 - Compute x/(2^n), for 0 <= n <= 30
 *  Round toward zero
 *   Examples: divpwr2(15,1) = 7, divpwr2(-33,4) = -2
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 2
 */
int divpwr2(int x, int n) {
  int signx = x >> 31; //get the sign of x
  int temp = (1 << n) + (~0); //get 1 << (n-1) in a special way; 2^n -1; (1 << n) + (~0) equals to ((1 << n) + (~1) + 1)
  int correct = signx & temp; //add bias when x is negative
  int result = (x + correct) >> n; //x has been corrected and can be shifted normally
  return result; 
}

/* 
 * isPositive - return 1 if x > 0, return 0 otherwise 
 *   Example: isPositive(-1) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 8
 *   Rating: 3
 */
int isPositive(int x) {
  int nsgn = !(x >> 31); // nsgn = !sgn: if sign is 1, then return 0
  return nsgn ^ !x ;
}

/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int signx = x >> 31;
  int signy = y >> 31;
  int tmp = (~y + x) >> 31;
  int same_sign = (!(signx ^ signy)) & tmp;
  int diff_sign = signx & (!signy);
  return same_sign | diff_sign;
}

/*
 * ilog2 - return floor(log base 2 of x), where x > 0
 *   Example: ilog2(16) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 90
 *   Rating: 4
 */
int ilog2(int x) {
/* Binary Search */
// use (!!x) as a representation of (x!=0)
	int result = (!!(x >> 16) << 4);
	result = result + ((!!(x >> (result + 8))) << 3);
	result = result + ((!!(x >> (result + 4))) << 2);
	result = result + ((!!(x >> (result + 2))) << 1);
	result = result + (!!(x >> (result + 1)));
	return result;
}

/* 
 * float_neg - Return bit-level equivalent of expression -f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representations of
 *   single-precision floating point values.
 *   When argument is NaN, return argument.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 10
 *   Rating: 2
 */
unsigned float_neg(unsigned uf) {
  unsigned result = uf ^ 0x80000000; // sign reverse
  unsigned isnan = uf & 0x7fffffff;
  if(isnan > 0x7f800000) // NaN
  	result = uf;
  return result;
}
```
