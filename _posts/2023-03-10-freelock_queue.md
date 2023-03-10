---
title: free-lock queue
tags: Algorithm
---

# Free-Lock Queue

## Single Producer and Single Consumer

### Algorithm

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
