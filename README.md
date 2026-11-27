# gz_till_release
GZ till release.


$portName = 'COM2'  

foreach ($baud in 2400, 9600, 4800, 1200, 19200, 38400) {
  Write-Host "=== Testing $baud baud (ESC Q A + Total light) ==="
  $p = New-Object System.IO.Ports.SerialPort $portName, $baud, None, 8, One
  $p.Open()
  $p.Write([byte[]](0x1B, 0x40), 0, 2)                                                  # ESC @
  Start-Sleep -Milliseconds 150
  $p.Write([byte[]](0x02, 0x4C, 0x30, 0x31, 0x30, 0x30), 0, 6)                          # STX L 0100 (Total only)
  $p.Write([byte[]](0x1B, 0x73, 0x32), 0, 3)                                            # ESC s 2 (Total only)
  $p.Write([byte[]](0x1B, 0x51, 0x41, 0x31,0x32,0x33,0x34,0x35,0x2E,0x36,0x37, 0x0D), 0, 12) # ESC Q A 12345.67 CR
  Start-Sleep -Milliseconds 200
  $p.Close()
  Start-Sleep -Seconds 2

  Write-Host "=== Testing $baud baud (plain CLR + 12345.67 CR, no ESC Q A) ==="
  $p = New-Object System.IO.Ports.SerialPort $portName, $baud, None, 8, One
  $p.Open()
  $p.Write([byte[]](0x0C, 0x31,0x32,0x33,0x34,0x35,0x2E,0x36,0x37, 0x0D), 0, 10)
  Start-Sleep -Milliseconds 200
  $p.Close()
  Start-Sleep -Seconds 2
}
