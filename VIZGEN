
import serial
import numpy as np
from time import strftime, localtime, sleep
import time
import struct
import socket
import os

# ser = serial.Serial(port='COM18',\
#                     baudrate=115200,\
#                     parity = 'N',\
#                     bytesize = 8,\
#                     stopbits = 1,\
#                     timeout = 0.3)


class VIZGEN:
    def cmd00(ser):
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 0
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            # ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(int(f'{readpackage[5]:08b}'[-1]))
                data.append(int(f'{readpackage[5]:08b}'[-2]))
                data.append(int(f'{readpackage[5]:08b}'[-3]))
                a = readpackage[6:9]
                a.append(0)
                data.append(struct.unpack('<I',bytearray(a))[0])
                for n in range(5):
                    data.append(struct.unpack('<I',bytearray(readpackage[4*n+9:4*n+13]))[0])
                for n in range(11):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+29:4*n+33]))[0])

            else:
                print(0)
                data = []
        
        return data

    def cmd01(viz, data): #set config value
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 1
        datalength = 68
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        
        package.append(int(str(data[0])+str(data[1])+str(data[2]),2))
        f = struct.pack('I',data[3])
        for n in range(3):
            package.append(f[n])
            
        for n in range(5):
            fdata = struct.pack("I",data[n+4])
            for i in range(len(fdata)):
                package.append(fdata[i])

        for n in range(11):
            fdata = struct.pack("f",data[n+9])
            for i in range(len(fdata)):
                package.append(fdata[i])
            
       
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(viz) == socket.socket:
            viz.send(package)
            ss = viz.recv(256)
        else:        
            viz.timeout = 2
            viz.write(package)
            # sleep(1)
            ss = viz.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd02(ser, data): # Time Setting (Real Time Clock)
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 2
        datalength = 6
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        for n in range(len(data)):
            package.append(data[n])
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:        
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response fom UUTr !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response

    def cmd03_400k(ser): # Measured Data
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 3
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.05
            ser.write(package)
            ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                for n in range(15):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+5:4*n+9]))[0])
            else:
                print(0)
                data = []
        
        return data[:6]
    

    """
    Measured Data Set
        Curent_I
        Voltage_I
        I_square
        Current_Q
        Voltage_Q
        V_square
        P_act
        Q_react
        P_del
        V
        I
        R
        X
        theta
        Rf_freq
        Curent_I_l
        Voltage_I_l
        I_square_l
        Current_Q_l
        Voltage_Q_l
        V_square_l
        P_act_l
        Q_react_l
        P_del_l
        V_l
        I_l
        R_l
        X_l
        theta_l
        Rf_freq_l
        board_temper
        sensor_temper
        sampling_freq
    """
    def cmd04(ser, data): # Measurement Start(=1) or Stop( = 0)
        ser.timeout = 0.3
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 4
        datalength = 1
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(data)

            
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
        
    def cmd05(ser, data): # Set sampling frequency.
        ser.timeout = 0.3
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 5
        datalength = 4
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(data)
        

        fdata = struct.pack("f",data)
        package.append(fdata)
            
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:        
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd06(ser, data): # Set Logging control
        ser.timeout = 0.3
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 6
        datalength = 24
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)

        package.append(data[0]) # mode at HF
        package.append(data[1]) # source at HF
        fdata = struct.pack("I",data[2]) # delay at HF
        package.append(fdata[:2])
        fdata = struct.pack("I",data[3]) # Count at HF
        package.append(fdata)
        fdata = struct.pack("f",data[4]) # threshold  at HF
        package.append(fdata)
        
        package.append(data[5]) # mode at LF
        package.append(data[6]) # source at LF 
        fdata = struct.pack("I",data[7]) # delay at LF
        package.append(fdata[:2])
        fdata = struct.pack("I",data[8]) # Count at LF
        package.append(fdata)
        fdata = struct.pack("f",data[9]) # threshold at LF
        package.append(fdata)
            
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:        
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd07(ser): # Get logged data info
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 7
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:     
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                for n in range(8):
                    data.append(struct.unpack('<I',bytearray(readpackage[4*n+5:4*n+9]))[0])
            else:
                print(0)
                data = []
        
        return data
    
    """
    status at HF
    in_cnt at HF
    req_cnt at HF
    got_cnt at HF
    status at LF
    in_cnt at LF
    req_cnt at LF
    got_cnt at LF
    """
    def cmd08(ser): # Get Heater Config
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 8
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
     
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                for n in range(4):
                    data.append(readpackage[n+5])
                data.append(struct.unpack('<I',bytearray(readpackage[9:13]))[0])
            else:
                print(0)
                data = []
        
        return data
    
    """
    uchar chip_select : 1, uchar avg_counter : 7, int 1 byte
    ch_sel
    voltage_limit
    current_limit
    update_period

    """    
    def cmd09(ser): # Req. register sensor
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 9
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:

            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(readpackage[5:13].decode())
            else:
                print(0)
                data = []
        
        return data
    
    """
    unchar sensor ID

    # """
    def cmd0a(ser): # Get Board info
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 10
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
   
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(readpackage[5:9].decode())
                data.append(readpackage[9:17].decode())
                data.append(readpackage[17:21].decode())
                data.append(readpackage[21:53].decode())

            else:
                print(0)
                data = []
        
        return data
    
    def cmd0b(ser): # Get Board info
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 11
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
 
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(readpackage[5].decode())
                data.append(readpackage[6].decode())
                data.append(readpackage[7].decode())
                data.append(readpackage[8].decode())
                data.append(struct.unpack('<I',bytearray(readpackage[9:13]))[0])
            else:
                print(0)
                data = []
        
        return data
    
    def cmd0c(ser, data): # Set Heater Config
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 12
        datalength = 8
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        
        
        package.append(data[0])
        package.append(data[1])
        package.append(data[2])
        package.append(data[3])
        fdata = struct.pack("I",data[4]) # delay at LF
        package.append(fdata)
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response    

    def cmd0d(ser): # Request measured User Data
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 13
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])

        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                for n in range(12):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+5:4*n+9]))[0])
            else:
                print(0)
                data = []
        
        return data
    
    def cmd0e(ser): # Get Report Data Config
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 14
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
    
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])

        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(struct.unpack('<I',bytearray(readpackage[5:9]))[0])
                for n in range(2):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+5:4*n+9]))[0])
                
                data.append(struct.unpack('<I',bytearray(readpackage[17:21]))[0])
                for n in range(2):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+17:4*n+21]))[0])
            else:
                print(0)
                data = []
        
        return data
    
    def cmd0f(ser, data): # Set Report Data Config
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 15
        datalength = 24
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)

        fdata = struct.pack("I",data[0]) # delay at LF
        package.append(fdata)
        fdata = struct.pack("f",data[2]) # delay at LF
        package.append(fdata)
        fdata = struct.pack("f",data[3]) # delay at LF
        package.append(fdata)
        fdata = struct.pack("I",data[4]) # delay at LF
        package.append(fdata)
        fdata = struct.pack("f",data[5]) # delay at LF
        package.append(fdata)
        fdata = struct.pack("f",data[6]) # delay at LF
        package.append(fdata)
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response 
    
    def cmd12(ser, data): # Set new FS for current RF frequency
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 18
        datalength = 4
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)

        fdata = struct.pack("f",data) # delay at LF
        package.append(fdata)
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd13(ser, data): # Set new FS for current RF frequency
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 19
        datalength = 44
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)

        fdata = struct.pack("I",data[0])
        package.append(fdata)
        for n in range(10):
            fdata = struct.pack("f",data[n+1])
            package.append(fdata)
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd14(ser, data): # Set new FS for current RF frequency
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 20
        datalength = 1
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)

        package.append(data)

        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd15(ser): # Get Report Data Config
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 21
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
     
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])

        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(struct.unpack('<I',bytearray(readpackage[5:9]))[0])
                for n in range(10):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+5:4*n+9]))[0])

            else:
                print(0)
                data = []
        
        return data

    def cmd1E(ser): # Get Report Data Config
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 30
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
     
            ser.timeout = 0.05
            ser.write(package)
            sleep(0.1)
            ss =  ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])

        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(int(f'{readpackage[5]:08b}'[-1]))
                data.append(int(f'{readpackage[5]:08b}'[-2]))
                data.append(int(f'{readpackage[5]:08b}'[-3]))
                a = readpackage[6:9]
                a.append(0)
                data.append(struct.unpack('<I',bytearray(a))[0])
                for n in range(5):
                    data.append(struct.unpack('<I',bytearray(readpackage[4*(n+1)+5:4*(n+1)+9]))[0])

            else:
                print(0)
                data = []
        
        return data
    
    def cmd1F(ser, data):
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 31
        datalength = 24
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        
        
        package.append(int(str(data[0])+str(data[1])+str(data[2]),2))
        f = struct.pack('<I',data[3])
        for n in range(3):
            package.append(f[n])
        
        for n in range(5):
            f = struct.pack('<I', data[n+4])
            for m in range(len(f)):
                package.append(f[m])

        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == socket.socket:
            ser.send(package)
            ss = ser.recv(256)
        else:
            ser.timeout = 0.3
            ser.write(package)
            ss = ss = ser.read(len(package))
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response        
    
"""
data.append(int(f'{readpackage[6]:08b}'[-4:]))

ser = serial.Serial(port='COM4',\
                    baudrate=115200,\
                    parity = 'N',\
                    bytesize = 8,\
                    stopbits = 1,\
                    timeout = 0.3)  

"""

class VIZSingle:
    
    def ENetData(IP,package):
        ser = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        ser.connect((IP,5000))
        ser.send(package)
        ss = ser.recv(256)
        ser.close()
        return ss
    
    
    def cmd00(ser):
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 0
        datalength = 0
        tail = 26

        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:
            ser.timeout = 0.05
            ser.write(package)
            ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                data.append(int(f'{readpackage[5]:08b}'[-1]))
                data.append(int(f'{readpackage[5]:08b}'[-2]))
                data.append(int(f'{readpackage[5]:08b}'[-3]))
                a = readpackage[6:9]
                a.append(0)
                data.append(struct.unpack('<I',bytearray(a))[0])
                for n in range(10):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+9:4*n+13]))[0])

            else:
                print(0)
                data = []
        
        return data

    def cmd01(ser, data): #set config value
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 1
        datalength = 44
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        
        package.append(int(str(data[0])+str(data[1])+str(data[2]),2))
        f = struct.pack('<I',data[3])
        for n in range(3):
            package.append(f[n])
            
        for n in range(10):
            fdata = struct.pack("<f",data[n+4])
            for i in range(len(fdata)):
                package.append(fdata[i])
            
       
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:        
            ser.timeout = 0.5
            ser.write(package)
            ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
    
    def cmd02(ser, data): # Time Setting (Real Time Clock)
        
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 2
        datalength = 6
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        for n in range(len(data)):
            package.append(data[n])
        
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:        
            ser.timeout = 0.5
            ser.write(package)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response fom UUTr !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response

    def cmd03(ser): # Measured Data
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 3
        datalength = 0
        tail = 26
        cksum = (dID + command + datalength)%256
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:
      
            ser.timeout = 0.04
            ser.write(package)
            ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
            
        if readpackage == []:
            print('No response from UUT !!')
            data = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                print('Received')
                data = []
                for n in range(18):
                    data.append(struct.unpack('<f',bytearray(readpackage[4*n+5:4*n+9]))[0])
            else:
                print(0)
                data = []
        
        return data

    """    
    for n in range(100):
        start = time.time()
        VIZSingle.cmd03(ser)
        print(time.time()-start)

    Measured Data Set
        Curent_I
        Voltage_I
        I_square
        Current_Q
        Voltage_Q
        V_square
        P_act
        Q_react
        P_del
        V
        I
        R
        X
        theta
        BD_temp
        Sensor_temp
        Rf_freq
    """
    def cmd04(ser, data): # Measurement Start(=1) or Stop( = 0)
        ser.timeout = 0.3
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 4
        datalength = 1
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(data)

            
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
        
    def cmd05(ser, data): # Set sampling frequency.
        ser.timeout = 0.3
        header0 = 22 
        header1 = 22
        dID = 48    
        command = 5
        datalength = 4
        tail = 26
        
        package = bytearray()
        package.append(header0)
        package.append(header1)
        package.append(dID)
        package.append(command)
        package.append(datalength)
        package.append(data)
        

        fdata = struct.pack("<f",data)
        for n in range(len(fdata)):
            package.append(fdata[n])
            
        cksum = np.sum(package[2:])%256
        package.append(cksum)
        package.append(tail)
    
        if type(ser) == str:
            ss = ENetData(ser, package)
        else:        
            ser.timeout = 0.3
            ser.write(package)
            sleep(0.1)
            ss = ss = ser.readall()
            
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage == []:
            print('No response from UUT !!')
            response = []
        else:
            if readpackage[-2] == np.sum(readpackage[2:-2])%256:
                response = 1
                print('Transmitted')
            else:
                response = 0
                print(0)
    
        return response
"""
lfgs = serial.Serial(port='COM12',\
                   baudrate=19200,\
                   parity = serial.PARITY_ODD,\
                   bytesize = 8,\
                   stopbits = 1)
""" 
class LFGS:


    def SetFreq(lfgs, freq):
        address = 1
        datalength = 4
        command = 18
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        f = struct.pack('<I',freq)
        for n in range(len(f)):
            package.append(f[n])
               
        cksum = 0
        for n in range(len(package)-1):
            cksum = cksum^package[n]
        package.append(cksum)
        
        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()

        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])

        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = "accepted"
        else:
            res = 'No response'

        return res

    def SetMode(lfgs, mode):
        address = 1
        datalength = 1
        command = 14
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        package.append(mode)
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)
        
        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        print(readpackage)

        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
        else:
            print('No response')

        return readpackage        
        

    def SetPower(lfgs, power):
        address = 1
        datalength = 2
        command = 8
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        f = struct.pack('H',power)
        for n in range(len(f)):
            package.append(f[n])
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)
        
        
        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = readpackage[3]
        else:
            print('No response')

        return res 

    def RFOn(lfgs):
        address = 1
        datalength = 0
        command = 2
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)

        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = readpackage[3]
        else:
            print('No response')

        return res  
 
    def RFOff(lfgs):
        address = 1
        datalength = 0
        command = 1
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)

        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = readpackage[3]
        else:
            print('No response')

        return res         
    
    def ForMon(lfgs):
        address = 1
        datalength = 0
        command = 165
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)

        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = struct.unpack('h', ss[3:5])[0]
        else:
            print('No response')

        return res      

    def RefMon(lfgs):
        address = 1
        datalength = 0
        command = 166
       
        package = bytearray()
        package.append(int(f'{(address):05b}'+f'{(datalength):03b}', 2))
        package.append(command)
        cksum = 0
        for n in range(len(package)):
            cksum = cksum^package[n]
        package.append(cksum)

        if type(lfgs) == str:
            ss = ENetData(lfgs, package)
        else:        
            lfgs.timeout = 0.1
            lfgs.write(package)
            ss = lfgs.readall()
        
        readpackage = []
        for c in range(len(ss)):
            readpackage.append(ss[c])
        
        if readpackage[0] == 6:
            ak = bytearray()
            ak.append(6)
            lfgs.write(ak)
            res = struct.unpack('h', ss[3:5])[0]
        else:
            print('No response')

        return res    
