Functions: 
bitAnd, bitXor, getByte, logicalShift, bitCount, bang, negate, tmin, isTmax

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
  return 2;
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
```
