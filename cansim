import can
import threading
import time

BUS_CHANNEL = "vcan0"

def ecu_sender(name, arbitration_id, extended=False, rtr=False):
    bus = can.Bus(
        interface="virtual",
        channel=BUS_CHANNEL,
        receive_own_messages=False
    )   

    msg = can.Message(
        arbitration_id=arbitration_id,
        data=[] if rtr else [0x11, 0x22, 0x33],
        is_extended_id=extended,
        is_remote_frame=rtr
    )

    time.sleep(1)  # allow both ECUs to start
    bus.send(msg)
    print(f"{name} SENT -> ID={hex(arbitration_id)}")

def ecu_receiver(name):
    bus = can.Bus(
        interface="virtual",
        channel=BUS_CHANNEL,
        receive_own_messages=True
    )

    while True:
        msg = bus.recv()
        print(
            f"{name} RX <- "
            f"ID={hex(msg.arbitration_id)} "
            f"EXT={msg.is_extended_id} "
            f"RTR={msg.is_remote_frame} "
            f"DATA={msg.data}"
        )

# ECU threads
t_rx = threading.Thread(target=ecu_receiver, args=("ECU_RX",), daemon=True)
t_tx1 = threading.Thread(target=ecu_sender, args=("ECU_LOW_ID", 0x100))
t_tx2 = threading.Thread(target=ecu_sender, args=("ECU_HIGH_ID", 0x300))

t_rx.start()
t_tx1.start()
t_tx2.start()

time.sleep(5)
