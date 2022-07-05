# bitcram

Binary serializer/deserializer in C

## Interface Idea 1

Packet Example

| Field   | Value |
|---------|-------|
| ID      | 1     |
| Command | 2     |
| Payload | 0x00  |
| CRC     | 0x7E  |

Packet Definition

``` c
#include "bitcram.h"

// packet definition
size_t num_fields = 4;
size_t field_sizes[num_fields] = {4, 4, 8, 8};
enum field_names {id, command, payload, crc};

// serialization
uint8_t data[size(field_sizes, num_fields)];
insert(data, field_sizes, num_fields, id, 1);
insert(data, field_sizes, num_fields, command, 2);
insert(data, field_sizes, num_fields, payload, 0);
insert(data, field_sizes, num_fields, crc, crc8(slice(data, id, payload)));

// deserialization

extract(data, field_sizes, num_fields, id);
extract(data, field_sizes, num_fields, command);
extract(data, field_sizes, num_fields, payload);
extract(data, field_sizes, num_fields, crc));
```

## Interface Idea 2

``` c
#include "bitcram.h"

// packet definition
size_t packet_size = 0;
id = add_field(packet_size, 4)
command = add_field(packet_size, 4)
payload = add_field(packet_size, 8)
crc = add_field(packet_size, 8)

// serialization
uint8_t packet[packet_size];
insert(packet, id, 1);
insert(packet, command, 2);
insert(packet, payload, 0x00);
insert(packet, crc, crc8(slice(packet, id, payload)));

// deserialization
printf("id: %d\n", extract(packet, id));
printf("command: %d\n", extract(packet, command));
printf("payload: %d\n", extract(packet, payload));
if (extract(packet, crc) == crc8(slice(packet, id, payload))) {
    printf("packet validated\n");
} else {
    printf("invalid packet\n");
}

// -------- bitcram.h -----------
typedef struct {size_t offset, width} field;

// define a new field for a packet
field add_field(size_t* packet_size, size_t field_size) {
    offset = *packet_size;
    *packet_size += field_size;
    return &field(offset, field_size);
}

// write to a specific field of a packet
void insert(uint8_t* packet, field insert_field, uint64_t data){...};

// extract a specific field from a packet into a void pointer of size n
int extract(uint8_t* packet, field extract_field, void * into, size_t n);


```

## Goals

- portability - no dependence on endianness, padding of system
- control over byte/bit endianness during serialization/deserialization
- header-only library


## See Also

- [bitpacker](https://github.com/CrustyAuklet/bitpacker)
