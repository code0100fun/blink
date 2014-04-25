def com_port
 '/dev/tty.usbmodem1411'
end

def baud_rate
  '57600'
end

def programmer
  'avr109'
end

def mcu
  'atmega32u4'
end

def hex_file
  'Blink.cpp.hex'
end

def arduino_dir
  '/Applications/Arduino.app'
end

def arduino_bin_dir
  "#{arduino_dir}/Contents/Resources/Java/hardware/tools/avr/bin"
end

def avr_gpp
  "#{arduino_bin_dir}/avr-g++"
end

def linker_flags
  '-c -g -Os -Wall -fno-exceptions -ffunction-sections -fdata-sections -mmcu=atmega32u4 -DF_CPU=8000000L -MMD -DUSB_VID=0x1B4F -DUSB_PID=0xF101 -DARDUINO=105'
end

task :compile do

end

task :link => [:compile] do

end

task :upload => [:link] do
  sh "avrdude -v -v -p #{mcu} -c #{programmer} -P #{com_port} -b #{baud_rate}  -D -Uflash:w:#{hex_file}:i"
end
