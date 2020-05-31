```c
/* 
 * bitAnd - x&y using only ~ and | 
 *   Example: bitAnd(6, 5) = 4
 *   Legal ops: ~ |
 *   Max ops: 8
 *   Rating: 1
 */
int bitAnd(int x, int y) {
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

```
