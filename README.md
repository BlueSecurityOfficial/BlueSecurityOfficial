import cv2
import time
import datetime

# The number "0" is the amount of webcams you have. 0 = 1 cam, 1 = 2 cams, and etc.
cap = cv2.VideoCapture(0)

face_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + "haarcascase_frontalface_default.xml")
body_cascade = cv2.CascadeClassifier(cv2.data.haarcascades + "haarcascase_fullbody.xml")


Security = False
Security_stopped_time = None
timer_starter = False
SECONDS_TO_RECORD_AFTER_DETECTION = 5
# This is how you set up the frame size. (Obviously)
frame_size = (int(cap.get(3)), int(cap.get(4)))
fourcc = cv2.VideoWriter_fourcc(*"mp4v")

# The underscore is a place-folder variable, so what you put instead of the underscore doesn't matter.
while True:
    _, frame = cap.read()

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    # The "1.3" is how accurate and how fast the video will be. The lower the number is, the more accurate, but slow.
    # The "5" is the amount of boxes the computer will use to recognize an actual face. Basically, don't worry too much.
    faces = face_cascade.detectMultiScale(gray, 1.3, 5)
    bodies = face_cascade.detectMultiScale(gray, 1.3, 5)

    if len(faces) + len(bodies) > 0:
       if Security:
           timer_started = False
       else:
           Security = True
           current_time = datetime.datetime.now().strftime("%d-%m-%Y-%H-%M-%S")
           out = cv2.VideoWriter(
               f"{current_time}.mp4", fourcc, 20, frame_size)
           print("Started Recording!")
    elif Security:
        if timer_started:
            if time.time() - Security_stopped_time >= SECONDS_TO_RECORD_AFTER_DETECTION:
               Security = False
               timer_started = False
               out.release()
               print("Stop Recording!")
        else:
          timer_started = True
          Security_stopped_time = time.time()

    out.write(frame)
    # for (x, y, width, height) in faces:
    #    cv2.rectangle(frame, (x, y), (x + width), (255, 0, 0), 3)

# The name "Camera" is just the title of the window that will pop up and record the victim.

    cv2.imshow("BlueSecurityCam", frame)
# This will make it possible to quit this frame, and not being stuck in a loop. To stop this frame, press the "q".
    if cv2.waitKey(1) == ord('q'):
        break

out.release
cap.release()
cv2.destroyAllWindows()
