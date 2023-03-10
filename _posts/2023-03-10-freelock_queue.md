---
title: free-lock queue
tags: Algorithm
---

# Free-Lock Queue

## Array-Based && Single-Producer-Single-Consumer

### Data Structure

The idea is we have a `ring_array` to store data, `read_index` pointing to the position which will be read and `write_index` pointing to the position which will be written.

Both index are always increasing, so according to unsigned integer specification, we can ensure `0 <= write_index - read_index <= MAX_SIZE`.

we can use `ring_array[read_index % MAX_SIZE]` to read data from Queue and `ring_array[write_index % MAX_SIZE]` to write data to Queue.

- `write_index - read_index == 0` means queue is empty.
- `write_index - read_index == MAX_SIZE` means queue is full.

if index overflow, because `MAX_SIZE` is a power of 2, we can ensure `((MAX_INDEX_VALUE % MAX_SIZE) + 1) % MAX_SIZE == 0 % MAX_SIZE == 0`. So the index even when overflowing is continuous.

### Example Code

```c
#include <stdbool.h>
#include <stdint.h>

typedef uint32_t FifoIndex;
#define FIFO_SIZE 1024U

struct DataPack {};

#define memoryfence __sync_synchronize()

struct Reader {
  FifoIndex readerIndex;
};
struct Writer {
  FifoIndex writerIndex;
  struct DataPack aData[FIFO_SIZE];
};

struct Reader reader;
struct Writer writer;

bool writeDataToFifo(const struct DataPack *pWrittenData) {
  bool result;
  memoryfence;
  FifoIndex size = writer.writerIndex - reader.readerIndex;
  if (size < FIFO_SIZE) {
    writer.aData[writer.writerIndex % FIFO_SIZE] = *pWrittenData;
    memoryfence;
    writer.writerIndex++;
    result = true;
  } else {
    /* full */
    result = false;
  }
  return result;
}
bool readDataFromFifo(struct DataPack *pReadedData) {
  bool result;
  memoryfence;
  FifoIndex size = writer.writerIndex - reader.readerIndex;
  if (size == 0U) {
    /* empty */
    result = false;
  } else if (size <= FIFO_SIZE) {
    *pReadedData = writer.aData[reader.readerIndex % FIFO_SIZE];
    memoryfence;
    reader.readerIndex++;
    result = true;
  } else {
    // error handler, fast move read index to write index
    reader.readerIndex = writer.writerIndex;
    result = false;
  }
  return result;
}

```
