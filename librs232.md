# librs232 Lua API Reference

The `librs232` Lua API provides a robust and easy-to-use interface for serial port communication. It bridges high-level Lua scripts with low-level C functions to enable cross-platform support for both POSIX (Linux/macOS) and Windows systems. The API allows for precise control over serial parameters, buffer management, and timed data transmission. The following description assumes the instantiation of a `librs232` Lua module via:

```lua
local rs232 = require("rs232")
```

## Module Functions
These core functions are used to initialize the communication and handle error diagnostics.

| Function                     | Parameters        | Returns                  | Description                                              |
| :--------------------------- | :---------------- | :----------------------- | :------------------------------------------------------- |
| `rs232.open(device)`         | `device` (string) | `error_code`, `port_obj` | Opens the serial port (e.g. `"/dev/ttyS0"` or `"COM1"`). |
| `rs232.error_tostring(code)` | `code` (int)      | `description` (string)   | Converts an error constant to a string.                  |

## Port Object Methods
These methods manage data transmission, buffer states, and the connection lifecycle.

| Method                  | Parameters                     | Returns                   | Description                                                  |
| :---------------------- | :----------------------------- | :------------------------ | :----------------------------------------------------------- |
| `port:read()`           | `len`, `[timeout]`, `[forced]` | `err`, `data`, `read_len` | Reads data. `forced=1` waits for full `len` or timeout (in ms). |
| `port:write()`          | `data`, `[timeout]`            | `err`, `written_len`      | Sends a string through the port with an optional timeout (in ms). |
| `port:flush()`          | none                           | `error_code`              | Clears both hardware and software I/O buffers.               |
| `port:in_queue()`       | none                           | `err`, `count`            | Returns the number of bytes waiting in the input buffer.     |
| `port:in_queue_clear()` | none                           | `error_code`              | Clears only the input (receive) buffer.                      |
| `port:device()`         | none                           | `path` (string)           | Returns the device path (e.g., `"/dev/ttyUSB0"` or `"COM1"`). |
| `port:fd()`             | none                           | `handle` (int)            | Returns the underlying system file descriptor.               |
| `port:close()`          | none                           | `error_code`              | Closes the port and releases system resources.               |

## Configuration Setters
All setter methods require exactly **one argument** from the specified list and return an **error code** (`rs232.RS232_ERR_*`).

| Method                    | Valid Arguments (Constants)                                  | Description                                    |
| :------------------------ | :----------------------------------------------------------- | :--------------------------------------------- |
| `port:set_baud_rate()`    | `rs232.RS232_BAUD_50`, `rs232.RS232_BAUD_75`, `rs232.RS232_BAUD_110`, `rs232.RS232_BAUD_134`, `rs232.RS232_BAUD_150`, `rs232.RS232_BAUD_200`, `rs232.RS232_BAUD_300`, `rs232.RS232_BAUD_600`, `rs232.RS232_BAUD_1200`, `rs232.RS232_BAUD_1800`, `rs232.RS232_BAUD_2400`, `rs232.RS232_BAUD_4800`, `rs232.RS232_BAUD_9600`, `rs232.RS232_BAUD_19200`, `rs232.RS232_BAUD_38400`, `rs232.RS232_BAUD_57600`, `rs232.RS232_BAUD_115200`, `rs232.RS232_BAUD_460800`, `rs232.RS232_BAUD_921600` | Sets the communication speed.                  |
| `port:set_data_bits()`    | `rs232.RS232_DATA_5`, `rs232.RS232_DATA_6`, `rs232.RS232_DATA_7`, `rs232.RS232_DATA_8` | Sets the number of bits per character.         |
| `port:set_stop_bits()`    | `rs232.RS232_STOP_1`, `rs232.RS232_STOP_2`                   | Sets the number of stop bits sent.             |
| `port:set_parity()`       | `rs232.RS232_PARITY_NONE`, `rs232.RS232_PARITY_ODD`, `rs232.RS232_PARITY_EVEN` | Sets the parity checking mode.                 |
| `port:set_flow_control()` | `rs232.RS232_FLOW_OFF`, `rs232.RS232_FLOW_HW`, `rs232.RS232_FLOW_XONXOFF` | Sets the hardware/software flow control.       |
| `port:set_dtr()`          | `rs232.RS232_DTR_OFF`, `rs232.RS232_DTR_ON`                  | Manually toggles the Data Terminal Ready line. |
| `port:set_rts()`          | `rs232.RS232_RTS_OFF`, `rs232.RS232_RTS_ON`                  | Manually toggles the Request To Send line.     |

## Configuration Getters & Status
These return the current numeric constant. Note: Although `_tostring` methods exist in the C-core, they are not exposed through the standard Lua wrapper object.

| Feature      | Getter Method         | Description                                    |
| :----------- | :-------------------- | :--------------------------------------------- |
| Baud Rate    | `port:baud_rate()`    | Returns the current `RS232_BAUD_*` constant.   |
| Data Bits    | `port:data_bits()`    | Returns the current `RS232_DATA_*` constant.   |
| Stop Bits    | `port:stop_bits()`    | Returns the current `RS232_STOP_*` constant.   |
| Parity       | `port:parity()`       | Returns the current `RS232_PARITY_*` constant. |
| Flow Control | `port:flow_control()` | Returns the current `RS232_FLOW_*` constant.   |
| DTR          | `port:dtr()`          | Returns the current `RS232_DTR_*` constant.    |
| RTS          | `port:rts()`          | Returns the current `RS232_RTS_*` constant.    |

## Error Constants Reference

This section lists the available error codes used by all methods to indicate success or failure.

| Constant Name                 | Value | Description                                              |
| ----------------------------- | ----- | -------------------------------------------------------- |
| `rs232.RS232_ERR_NOERROR`     | 0     | Operation completed successfully.                        |
| `rs232.RS232_ERR_UNKNOWN`     | 1     | An unspecified error occurred.                           |
| `rs232.RS232_ERR_OPEN`        | 2     | Failed to open the serial device.                        |
| `rs232.RS232_ERR_CLOSE`       | 3     | Failed to close the serial device properly.              |
| `rs232.RS232_ERR_FLUSH`       | 4     | Failed to flush the I/O buffers.                         |
| `rs232.RS232_ERR_CONFIG`      | 5     | Invalid configuration or failed to apply settings.       |
| `rs232.RS232_ERR_READ`        | 6     | Error occurred during a read operation.                  |
| `rs232.RS232_ERR_WRITE`       | 7     | Error occurred during a write operation.                 |
| `rs232.RS232_ERR_SELECT`      | 8     | Error during internal polling/waiting (select call).     |
| `rs232.RS232_ERR_TIMEOUT`     | 9     | The operation timed out before completion.               |
| `rs232.RS232_ERR_IOCTL`       | 10    | Low-level I/O control operation failed.                  |
| `rs232.RS232_ERR_PORT_CLOSED` | 11    | Attempted operation on a port that is not open.          |
| `rs232.RS232_ERR_BREAK`       | 12    | A break condition was detected on the line.              |
| `rs232.RS232_ERR_FRAME`       | 13    | A framing error occurred (invalid stop bit).             |
| `rs232.RS232_ERR_PARITY`      | 14    | A parity error was detected in the received data.        |
| `rs232.RS232_ERR_RXOVERFLOW`  | 15    | The receive buffer has overflowed.                       |
| `rs232.RS232_ERR_OVERRUN`     | 16    | Data was overrun (new byte arrived before old was read). |

## Examples
Below are practical examples illustrating how to use the API for common tasks.

### Sending a Command
```lua
local rs232 = require("rs232")
local e, port = rs232.open("COM1")

if e == rs232.RS232_ERR_NOERROR then
    port:set_baud_rate(rs232.RS232_BAUD_115200)
    local err, written = port:write("GET_STATUS\n", 100)
    if err ~= rs232.RS232_ERR_NOERROR then
        print("Write failed: " .. rs232.error_tostring(err))
    end
    port:close()
end
```

### Receiving a Fixed-Length Packet

```lua
local rs232 = require("rs232")
local e, port = rs232.open("/dev/ttyUSB0")

if e == rs232.RS232_ERR_NOERROR then
    -- Wait for a 10-byte packet with a 2-second timeout
    -- forced = 1 ensures we wait until all bytes arrive or timeout occurs
    local err, data, len = port:read(10, 2000, 1)
    if err == rs232.RS232_ERR_NOERROR then
        print("Received: " .. data)
    end
    port:close()
end
```

## License

See `https://github.com/OneLuaPro/librs232/blob/master/COPYING`.