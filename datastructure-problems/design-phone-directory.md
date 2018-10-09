# Design Phone Directory

---
_update 2018-10-08 23:40:06_

Design a Phone Directory which supports the following operations:

    get: Provide a number which is not assigned to anyone.
    check: Check if a number is available or not.
    release: Recycle or release a number.

**Example:**

    // Init a phone directory containing a total of 3 numbers: 0, 1, and 2.
    PhoneDirectory directory = new PhoneDirectory(3);

    // It can return any available phone number. Here we assume it returns 0.
    directory.get();

    // Assume it returns 1.
    directory.get();

    // The number 2 is available, so return true.
    directory.check(2);

    // It returns 2, the only number that is left.
    directory.get();

    // The number 2 is no longer available, so return false.
    directory.check(2);

    // Release number 2 back to the pool.
    directory.release(2);

    // Number 2 is available again, return true.
    directory.check(2);

<br/>

#### Basic Idea:
使用一个set记录released的号码，使用一个max记录所有号码的最大值，用一个curr来指下一个顺序分配的号码。在get的时候，优先分配set中之前被release的号码，然后再顺序分配curr++。

* #### Java Code:
  ```java
  class PhoneDirectory {
      private int curr;
      private int max;
      private Set<Integer> set;
      /** Initialize your data structure here
          @param maxNumbers - The maximum numbers that can be stored in the phone directory. */
      public PhoneDirectory(int maxNumbers) {
          curr = 0;
          max = maxNumbers - 1;
          set = new HashSet<>();
      }

      /** Provide a number which is not assigned to anyone.
          @return - Return an available number. Return -1 if none is available. */
      public int get() {
          Iterator<Integer> it = set.iterator();
          if (! it.hasNext()) {
              if (curr <= max) return curr++;
              else return -1;
          } else {
              int ret = it.next();
              it.remove();
              return ret;
          }
      }

      /** Check if a number is available or not. */
      public boolean check(int number) {
          return set.contains(number) || number >= curr;
      }

      /** Recycle or release a number. */
      public void release(int number) {
          if (! check(number)) set.add(number);
      }
  }
  ```
