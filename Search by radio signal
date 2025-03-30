import serial
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime
import os
sum = np.zeros(5)
PORT = "/dev/ttyACM0"
START = 2400000000
STOP = 2500000000
STEP = 200000
ATTENUATION = 0
POINT_TIMEOUT = 200
POINT_ADC_SAMPLES = 20
INTERMEDIATE_FREQ = 10700000
GEN_FREQ = 1100000000
GEN_OUTPUT = -25
command_index = 0
s = serial.Serial(PORT, baudrate=115200, timeout=1)

while True:
    while True:
        try:
            def generator_on(serial_port, command_number):
                serial_port.write(f"gon {command_number}\n".encode())
                command_number += 1
                serial_port.readline()
                serial_port.reset_input_buffer()
                serial_port.reset_output_buffer()
            def generator_off(serial_port, command_number):
                serial_port.write(f"gof {command_number}\n".encode())
                command_number += 1
                serial_port.readline()
                serial_port.reset_input_buffer()
                serial_port.reset_output_buffer()
            def generator_set_output(serial_port, output_amplitude, command_number):
                command_att = (100 * 100) + ((output_amplitude + 15) * 100)  # format generator output for command
                serial_port.write(f"sga {command_att} {command_number}\r\n".encode())
                command_number += 1
                serial_port.readline()
                serial_port.reset_input_buffer()
                serial_port.reset_output_buffer()
            def generator_set_frequency(serial_port, frequency, command_number):
                serial_port.write(f"scf {frequency} {command_number}\r\n".encode())
                command_number += 1
                serial_port.readline()
                serial_port.reset_input_buffer()
                serial_port.reset_output_buffer()
            def scan_data(serial_port, Start, Stop, Step, Attenuation, intermid_freq, adc_samples, timeout,
                          command_number,
                          tracking):
                amplitude_result = np.zeros(int((Stop - Start) / Step) + 1)
                directory = r"/home/orlovsg/Desktop/arst"
                filename = datetime.now().strftime("file_%Y-%m-%d_%H-%S")
                path = directory+filename+".txt."
                
                file.write(str(amplitude_result))
                   
                command_att = (100 * 100) + (Attenuation * 100)
                if not tracking:
                    serial_port.write(
                        f"scn20 {Start} {Stop} {Step} {timeout} {adc_samples} {intermid_freq} {command_att} {command_number}\r\n".encode())
                else:
                    serial_port.write(
                        f"scn22 {Start} {Stop} {Step} {timeout} {adc_samples} {intermid_freq} {command_att} {command_number}\r\n".encode())
                command_number += 1
                serial_port.readline()
                serial_port.readline()
                scan_ans = serial_port.readline()
                serial_port.readline()
                serial_port.reset_input_buffer()
                serial_port.reset_output_buffer()
                index = len(scan_ans) - 1
                while scan_ans[index] != 255:
                    index -= 1
                index -= 2
                stopindex = index
                iterator = 0
                index = 0
                while index < stopindex:
                    binaryval = (scan_ans[index] << 8) + scan_ans[index + 1]
                    for i in range(11, 16):
                        binaryval &= ~(1 << i)
                    amplitude_result[iterator] = -Attenuation + ((10.0 * 80 - binaryval) / 10.0)
                    #if amplitude_result[iterator]< -80:
                    #    amplitude_result[iterator]=-110
                    iterator += 1
                    index += 2

                for i in range(5):
                    sum[i]=0
                    for j in range(i * 100, (i + 1) * 100):
                        sum[i] += amplitude_result[j]
                      #  print(amplitude_result[j], sum[i])
                print(sum/100)
                return amplitude_result
            plt.figure('Spectrum')
            generator_on(s, command_index)
            generator_set_output(s, GEN_OUTPUT, command_index)
            generator_set_frequency(s, GEN_FREQ, command_index)

            freq_range = np.linspace(START, STOP, int((STOP - START) / STEP) + 1)
            iteration_counter = 100
            while iteration_counter != 0:
                amplitude_data = scan_data(s, START, STOP, STEP, ATTENUATION,
                                           INTERMEDIATE_FREQ, POINT_ADC_SAMPLES, POINT_TIMEOUT,
                                           command_index, False)
                for amplitude in amplitude_data:
                    if amplitude < -70000:
                        print("Дрон")
                command_index += 1

        except IndexError:
            break



