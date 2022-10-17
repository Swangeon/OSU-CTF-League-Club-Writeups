### Notice
**The code will be different from what you might see as during the challenge I changed the names of things**

## What we know about the file
- After running `file`:
```
popquiz: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=744d0c4a736c1f1f128119d8af26ddff5c1250ff, not stripped
```
- After running `s/ltrace`:
```
execve("./popquiz", ["./popquiz"], 0x7ffdeb439ec0 /* 49 vars */) = 0
brk(NULL)                               = 0x5590d5612000
arch_prctl(0x3001 /* ARCH_??? */, 0x7fff0ab7b390) = -1 EINVAL (Invalid argument)
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=93220, ...}) = 0
mmap(NULL, 93220, PROT_READ, MAP_PRIVATE, 3, 0) = 0x7f0b46241000
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\300A\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
pread64(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32, 848) = 32
pread64(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\30x\346\264ur\f|Q\226\236i\253-'o"..., 68, 880) = 68
fstat(3, {st_mode=S_IFREG|0755, st_size=2029592, ...}) = 0
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f0b4623f000
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
pread64(3, "\4\0\0\0\20\0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0", 32, 848) = 32
pread64(3, "\4\0\0\0\24\0\0\0\3\0\0\0GNU\0\30x\346\264ur\f|Q\226\236i\253-'o"..., 68, 880) = 68
mmap(NULL, 2037344, PROT_READ, MAP_PRIVATE|MAP_DENYWRITE, 3, 0) = 0x7f0b4604d000
mmap(0x7f0b4606f000, 1540096, PROT_READ|PROT_EXEC, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x22000) = 0x7f0b4606f000
mmap(0x7f0b461e7000, 319488, PROT_READ, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x19a000) = 0x7f0b461e7000
mmap(0x7f0b46235000, 24576, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_DENYWRITE, 3, 0x1e7000) = 0x7f0b46235000
mmap(0x7f0b4623b000, 13920, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0) = 0x7f0b4623b000
close(3)                                = 0
arch_prctl(ARCH_SET_FS, 0x7f0b46240540) = 0
mprotect(0x7f0b46235000, 16384, PROT_READ) = 0
mprotect(0x5590d4a01000, 4096, PROT_READ) = 0
mprotect(0x7f0b46285000, 4096, PROT_READ) = 0
munmap(0x7f0b46241000, 93220)           = 0
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}) = 0
brk(NULL)                               = 0x5590d5612000
brk(0x5590d5633000)                     = 0x5590d5633000
write(1, "1\n", 21
)                      = 2
write(1, "POP QUIZ TIME! Can you solve the"..., 58POP QUIZ TIME! Can you solve these challenging questions?
) = 58
fstat(0, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}) = 0
write(1, "What number am I thinking of? ", 30What number am I thinking of? ) = 30
read(0, 0x5590d56126b0, 1024)           = ? ERESTARTSYS (To be restarted if SA_RESTART is set)
--- SIGWINCH {si_signo=SIGWINCH, si_code=SI_KERNEL} ---
read(0, 0x5590d56126b0, 1024)           = ? ERESTARTSYS (To be restarted if SA_RESTART is set)
--- SIGWINCH {si_signo=SIGWINCH, si_code=SI_KERNEL} ---

Starts to read input here
===================================================
printf("%d\n", 11
)                                                                                                            = 2
puts("POP QUIZ TIME! Can you solve the"...POP QUIZ TIME! Can you solve these challenging questions?
)                                                                                  = 58
printf("What number am I thinking of? ")                                                                                     = 30
__isoc99_scanf(0x55d4dd600e27, 0x7ffcc95d9f10, 0, 0What number am I thinking of? inputhere
```
- Now we can open this up using Ghidra and go to the main function
```C
int main(void)
{
  int ans;
  
  printf("%d\n",1);
  puts("POP QUIZ TIME! Can you solve these challenging questions?");
  ans = question_1();
  if (((ans != 0) && (ans = question_2(), ans != 0)) && (ans = question_3(), ans != 0)) {
    readFlag();
    return 0;
  }
  puts("WRONG");
  return 1;
}
```
- We care about 4 functions
	- question_1 through 3
	- readFlag
		- This will be for the server side so we do not have to worry about that locally
- The code block below sets the `int ans` to the return of the `question_1` function and asks that in order to get the flag we want all of the functions must return a number that is not zero
```C
ans = question_1();
if (((ans != 0) && (ans = question_2(), ans != 0)) && (ans = question_3(), ans != 0)) {
	readFlag();
	return 0;
}
```
- Lets start looking at the functions

## question_1
```C
bool question_1(void)
{
  long in_FS_OFFSET;
  int user_in;
  int local_14;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  local_14 = 0x2325;
  printf("What number am I thinking of? ");
  __isoc99_scanf(&DAT_00100e27,&user_in);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return local_14 == user_in;
}
```
- What we are really interested in here is the variable `local_14` as it is compared in a boolean way to our `user_in` variable that takes input to the user
- So what does it mean that `local_14 = 0x2325;`?
	- 0x2325 = 8997
- question_1 answer is `8997`

## question_2
```C
bool question_2(void)
{
  long in_FS_OFFSET;
  char user_in;
  char cStack19;
  char cStack18;
  char cStack17;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  printf("Gimme some characters: ");
  _user_in = 0;
  __isoc99_scanf("\n%c%c%c%c",&user_in,&cStack19,&cStack18,&cStack17);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return (int)cStack17 + (int)user_in + (int)cStack19 + (int)cStack18 == 0x1a0;
}
```
- So we can see that we are setting up 4 characters that will be assigned by the `scanf()` function
- We can then see that at the return statement we take the 4 inputted chars get typecasted to ints and that, when summed, they need to equal `0x1a0` which equals 416
- A thing about C is that chars in C are actually ints as they are from the ascii table so if we take 416 and divide it by 4 (remember we take in 4 chars) we get 104
	- 104 in the ascii table is `h`
- question_2 answer is `hhhh`

## question_3
```C
void question_3(void)
{
  long in_FS_OFFSET;
  int user_in;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  puts("???");
  user_in = 0;
  __isoc99_scanf(&DAT_00100e27,&user_in);
  check(user_in);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
```
- So the immediate thing that stands out is the function `check` that takes in user input
```C
undefined8 check(uint param_1)
{
  int iVar1;
  int str_len;
  size_t tempstr_len;
  undefined8 res;
  long thirty_zeros;
  undefined8 *pStr;
  long in_FS_OFFSET;
  int i;
  int local_124;
  undefined8 original_str;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  pStr = &original_str;
  for (thirty_zeros = 0x1f; thirty_zeros != 0; thirty_zeros = thirty_zeros + -1) {
    *pStr = 0;
    pStr = pStr + 1;
  }
  *(undefined4 *)pStr = 0;
  *(undefined2 *)((long)pStr + 4) = 0;
  *(undefined *)((long)pStr + 6) = 0;
  sprintf((char *)&original_str,"%d",(ulong)param_1);
  tempstr_len = strnlen((char *)&original_str,0xff);
  str_len = (int)tempstr_len;
  if ((tempstr_len & 1) == 0) {
    if (str_len < 4) {
      res = 0;
    }
    else {
      for (i = 1; iVar1 = str_len / 2, i < str_len / 2; i = i + 1) {
        if (*(char *)((long)&original_str + (long)i) <=
            *(char *)((long)&original_str + (long)(i + -1))) {
          res = 0;
          goto LAB_00100baf;
        }
      }
      do {
        local_124 = iVar1 + 1;
        if (str_len <= local_124) {
          res = 1;
          goto LAB_00100baf;
        }
        thirty_zeros = (long)iVar1;
        iVar1 = local_124;
      } while (*(char *)((long)&original_str + (long)local_124) <
               *(char *)((long)&original_str + thirty_zeros));
      res = 0;
    }
  }
  else {
    res = 0;
  }
LAB_00100baf:
  if (local_10 == *(long *)(in_FS_OFFSET + 0x28)) {
    return res;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail();
}
```
- The first things we can see is a bunch of variables that get defined
- The next block of code is just setting the `original_str` variable to be initialized to all zeros through the `pStr` pointer so it's not vitally important
```C
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  pStr = &original_str;
  for (thirty_zeros = 0x1f; thirty_zeros != 0; thirty_zeros = thirty_zeros + -1) {
    *pStr = 0;
    pStr = pStr + 1;
  }
  *(undefined4 *)pStr = 0;
  *(undefined2 *)((long)pStr + 4) = 0;
  *(undefined *)((long)pStr + 6) = 0;
```
- Next we are taking the user_in froim the `question_3` (being `param_1` in this case) function and assigning it to `original_str` and then getting its length until we get a `\0` since strings in C are terminated with that
```C
  sprintf((char *)&original_str,"%d",(ulong)param_1);
  tempstr_len = strnlen((char *)&original_str,0xff);
  str_len = (int)tempstr_len;
```
- Next we get a big chuck of code
```C
  if ((tempstr_len & 1) == 0) {
    if (str_len < 4) {
      res = 0;
    }
    else {
      for (i = 1; iVar1 = str_len / 2, i < str_len / 2; i = i + 1) {
        if (*(char *)((long)&original_str + (long)i) <=
            *(char *)((long)&original_str + (long)(i + -1))) {
          res = 0;
          goto LAB_00100baf;
        }
      }
      do {
        local_124 = iVar1 + 1;
        if (str_len <= local_124) {
          res = 1;
          goto LAB_00100baf;
        }
        thirty_zeros = (long)iVar1;
        iVar1 = local_124;
      } while (*(char *)((long)&original_str + (long)local_124) <
               *(char *)((long)&original_str + thirty_zeros));
      res = 0;
    }
  }
  else {
    res = 0;
  }
LAB_00100baf:
  if (local_10 == *(long *)(in_FS_OFFSET + 0x28)) {
    return res;
  }
```
### Breakdown
1. We want to make sure we have a string `if ((tempstr_len & 1) == 0)`
2. We want to make sure the strings length is greater than 4
3. If our string length is greater than 4, we go into a else clause and into a for loop that, when broken down:
	1. We only want to go through the first half of the string
	2. From the beginning of the string to the half point
		1. if a current character is less than the one that comes before it , return 0 and we fail
4. Next, we go into a `do, while` loop (This is only going to cover the first iteration of the loop but the concepts still apply to all other iterations)
	1. Taking the variable `local_124` and makes it equal to what we had asssigned as the midpoint being `iVar1` and adding 1 to it
	2. If the `local_124` is greater than or equal to the string length, we have succeeded
	3. However, if not, we take our initialization variable and make it equal to our half and our half variable will now equal to `local_124`
	4. Lastly, we keep the `do, while` loop going so long as what is at the mid + 1 is less than what is at the mid

### Back to it
- So from this breakdown we probably want an even number of numbers (just to make it easier on ourselves for input)
	- In my case I wanted 4 chars
- so fwe need the first half to go in ascending order and then the last half to be in decsending order so I choose `1221`
- question_3 answer is `1221`
