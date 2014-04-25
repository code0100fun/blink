def project_name
  app_directory.split('/').last
end

def app_directory
  File.expand_path(File.dirname __FILE__)
end

def com_port
 '/dev/tty.usbmodem1d1131'
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

def build_dir
  "#{app_directory}/build"
end

def arduino_app_dir
  '/Applications/Arduino.app'
end

def arduino_hardware_dir
  "#{arduino_app_dir}/Contents/Resources/Java/hardware"
end

def arduino_include_dir
  "#{arduino_hardware_dir}/arduino/cores/arduino"
end

def arduino_bin_dir
  "#{arduino_hardware_dir}/tools/avr/bin"
end

def avr_gpp
  "#{arduino_bin_dir}/avr-g++"
end

def avr_gcc
  "#{arduino_bin_dir}/avr-gcc"
end

def avr_ar
  "#{arduino_bin_dir}/avr-ar"
end

def avr_objcopy
  "#{arduino_bin_dir}/avr-objcopy"
end

def c_flags
  '-c -g -Os -Wall -ffunction-sections -fdata-sections -mmcu=atmega32u4 -DF_CPU=8000000L -MMD -DUSB_VID=0x1B4F -DUSB_PID=0xF101 -DARDUINO=105'
end

def cpp_flags
  '-c -g -Os -Wall -fno-exceptions -ffunction-sections -fdata-sections -mmcu=atmega32u4 -DF_CPU=8000000L -MMD -DUSB_VID=0x1B4F -DUSB_PID=0xF101 -DARDUINO=105'
end

def eeprom_flags
  '-O ihex -j .eeprom --set-section-flags=.eeprom=alloc,load --no-change-warnings --change-section-lma .eeprom=0'
end

def hex_flags
  '-O ihex -R .eeprom'
end

def include_directories
  [arduino_include_dir, '/Users/dev/Documents/Arduino/hardware/SF32u4_boards/variants/promicro']
end

def c_include_flags
  include_directories.map { |include| "-I#{include}"}.join(' ')
end

def avrdude
  "#{arduino_bin_dir}/avrdude"
end

def avrdude_config
  "#{arduino_hardware_dir}/tools/avr/etc/avrdude.conf"
end

def arduino_libc_dir
  "#{arduino_hardware_dir}/arduino/cores/arduino"
end

def app_c
  Dir[app_directory + '/src/*.cpp']
end

def avr_libc_dir
  "#{arduino_libc_dir}/avr-libc"
end

def avr_libc
  ['malloc.c', 'realloc.c'].map { |filename| "#{avr_libc_dir}/#{filename}" }
end

def compiler(filename)
  {
    "c" => avr_gcc,
    "cpp" => avr_gpp
  }[filename.split('.').last]
end

def compiler_flags(filename)
  {
    "c" => c_flags,
    "cpp" => cpp_flags
  }[filename.split('.').last]
end

def linker_flags
  "-Os -Wl,--gc-sections -mmcu=atmega32u4"
end

def output_extension(filename)
  {
    "c" => "o",
    "cpp" => "o"
  }[filename.split('.').last]
end

def arduino_libc
  Dir[arduino_libc_dir + '/*.c'] +
  Dir[arduino_libc_dir + '/*.cpp']
end

def c_files
  app_c + avr_libc + arduino_libc
end

task :build_dir do
  Dir.mkdir(build_dir) unless Dir.exists?(build_dir)
end

task :compile => [:build_dir] do
  c_files.each do |filename|
    output = "#{build_dir}/#{filename.split('/').last}.#{output_extension(filename)}"
    sh "#{compiler(filename)} #{compiler_flags(filename)} #{c_include_flags} #{filename} -o #{output}"
  end
end

task :archive => [:compile] do
  (Dir[build_dir + '/*.o'] - [main_object_file]).each do |filename|
    sh "#{avr_ar} rcs #{archive_file} #{filename}"
  end
end

def elf_file
  "#{build_dir}/#{project_name}.elf"
end

def eeprom_file
  "#{build_dir}/#{project_name}.eep"
end

def hex_file
  "#{build_dir}/#{project_name}.hex"
end

def main_object_file
  build_dir + "/" + project_name + ".cpp.o"
end

def archive_file
  build_dir + '/core.a'
end

task :link => [:archive] do
  sh "#{avr_gcc} #{linker_flags} -o #{elf_file} #{main_object_file} #{archive_file} -L #{build_dir} -lm"
end

task :eeprom => [:link] do
  sh "#{avr_objcopy} #{eeprom_flags} #{elf_file} #{eeprom_file}"
end

task :hex => [:link] do
  sh "#{avr_objcopy} #{hex_flags} #{elf_file} #{hex_file}"
end

task :clean do
  puts "removing build dir"
  FileUtils.rm_rf(build_dir)
end

task :upload => [:build] do
  sh "#{avrdude} -C #{avrdude_config} -v -v -v -v -p #{mcu} -c #{programmer} -P #{com_port} -b #{baud_rate}  -D -Uflash:w:#{hex_file}:i"
end

task :console do
  sh "screen #{com_port} 9600"
end

task :build => [:hex, :eeprom]

task :default => :build
