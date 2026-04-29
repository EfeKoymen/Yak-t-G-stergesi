import tkinter as tk from tkinter import ttk import math import time

\# ----------------------------- # CESSNA
172 FUEL GAUGE SIMULATOR #
-----------------------------

MAX\_FUEL\_PER\_TANK = 26.0 # gallons UPDATE\_MS = 100 # animation update
interval TIME\_SCALE = 60 # 1 real second = 60 simulated seconds

class FuelGaugeApp: def \_\_init\_\_(self, root): self.root = root
self.root.title("Cessna 172 Fuel Quantity Indicator")

self.left\_fuel = 26.0 self.right\_fuel = 26.0 self.running = False
self.last\_time = None

self.canvas = tk.Canvas(root, width=520, height=520, bg="#1b1b1b")
self.canvas.grid(row=0, column=0, rowspan=10, padx=15, pady=15)

control\_frame = ttk.Frame(root) control\_frame.grid(row=0, column=1,
sticky="n", padx=15, pady=15)

ttk.Label(control\_frame, text="Left Tank Initial Fuel
(gal)").grid(row=0, column=0, sticky="w") self.left\_entry =
ttk.Entry(control\_frame) self.left\_entry.insert(0, "26")
self.left\_entry.grid(row=1, column=0, pady=5)

ttk.Label(control\_frame, text="Right Tank Initial Fuel
(gal)").grid(row=2, column=0, sticky="w") self.right\_entry =
ttk.Entry(control\_frame) self.right\_entry.insert(0, "26")
self.right\_entry.grid(row=3, column=0, pady=5)

ttk.Label(control\_frame, text="RPM").grid(row=4, column=0,
sticky="w") self.rpm\_slider = ttk.Scale(control\_frame, from\_=800,
to=2700, orient="horizontal") self.rpm\_slider.set(2200)
self.rpm\_slider.grid(row=5, column=0, pady=5)

self.rpm\_label = ttk.Label(control\_frame, text="RPM: 2200")
self.rpm\_label.grid(row=6, column=0, sticky="w")

self.consumption\_label = ttk.Label(control\_frame, text="Fuel Flow: 0.0
gal/h") self.consumption\_label.grid(row=7, column=0, sticky="w",
pady=5)

self.left\_label = ttk.Label(control\_frame, text="Left: 26.0 gal")
self.left\_label.grid(row=8, column=0, sticky="w")

self.right\_label = ttk.Label(control\_frame, text="Right: 26.0 gal")
self.right\_label.grid(row=9, column=0, sticky="w")

ttk.Button(control\_frame, text="START",
command=self.start).grid(row=10, column=0, sticky="ew", pady=10)
ttk.Button(control\_frame, text="PAUSE",
command=self.pause).grid(row=11, column=0, sticky="ew", pady=5)
ttk.Button(control\_frame, text="RESET",
command=self.reset).grid(row=12, column=0, sticky="ew", pady=5)

self.rpm\_slider.configure(command=self.update\_rpm\_label)

self.draw\_static\_gauge() self.update\_gauge()

def update\_rpm\_label(self, value=None): rpm = self.rpm\_slider.get()
self.rpm\_label.config(text=f"RPM: {rpm:.0f}")

def fuel\_flow\_rate(self, rpm): """ Basitleştirilmiş Cessna 172 yakıt
tüketim modeli. RPM arttıkça galon/saat tüketim artar. """ if rpm <
1000: return 2.0 elif rpm < 1800: return 4.5 elif rpm < 2200: return
7.0 elif rpm < 2500: return 8.5 else: return 10.0

def fuel\_to\_angle\_left(self, fuel): fuel = max(0, min(MAX\_FUEL\_PER\_TANK,
fuel)) ratio = fuel / MAX\_FUEL\_PER\_TANK

empty\_angle = 230 full\_angle = 140

return empty\_angle + ratio \* (full\_angle - empty\_angle)

def fuel\_to\_angle\_right(self, fuel): fuel = max(0,
min(MAX\_FUEL\_PER\_TANK, fuel)) ratio = fuel / MAX\_FUEL\_PER\_TANK

empty\_angle = -50 full\_angle = 40

return empty\_angle + ratio \* (full\_angle - empty\_angle)

def draw\_static\_gauge(self): self.canvas.delete("all")

cx, cy = 260, 260 radius = 220

self.canvas.create\_oval( cx - radius, cy - radius, cx + radius, cy +
radius, fill="#1f2f5f", outline="black", width=10 )

self.canvas.create\_text(cx, 65, text="FUEL", fill="white",
font=("Arial", 28, "bold")) self.canvas.create\_text(cx, 455,
text="QTY", fill="white", font=("Arial", 24, "bold"))
self.canvas.create\_text(cx, 425, text="GALLONS", fill="white",
font=("Arial", 10))

self.canvas.create\_text(85, 255, text="L\\nE\\nF\\nT", fill="white",
font=("Arial", 18, "bold")) self.canvas.create\_text(435, 255,
text="R\\nI\\nG\\nH\\nT", fill="white", font=("Arial", 18,
"bold"))

for value in \[0, 5, 10, 15, 20, 26]: self.draw\_mark\_left(value)
self.draw\_mark\_right(value)

def draw\_mark\_left(self, value): angle =
math.radians(self.fuel\_to\_angle\_left(value)) cx, cy = 260, 260

r1 = 110 r2 = 145

x1 = cx + r1 \* math.cos(angle) y1 = cy - r1 \* math.sin(angle) x2 =
cx + r2 \* math.cos(angle) y2 = cy - r2 \* math.sin(angle)

self.canvas.create\_line(x1, y1, x2, y2, fill="white", width=4)

tx = cx + 170 \* math.cos(angle) ty = cy - 170 \* math.sin(angle)
self.canvas.create\_text(tx, ty, text=str(value), fill="white",
font=("Arial", 15, "bold"))

def draw\_mark\_right(self, value): angle =
math.radians(self.fuel\_to\_angle\_right(value)) cx, cy = 260, 260

r1 = 110 r2 = 145

x1 = cx + r1 \* math.cos(angle) y1 = cy - r1 \* math.sin(angle) x2 =
cx + r2 \* math.cos(angle) y2 = cy - r2 \* math.sin(angle)

self.canvas.create\_line(x1, y1, x2, y2, fill="white", width=4)

tx = cx + 170 \* math.cos(angle) ty = cy - 170 \* math.sin(angle)
self.canvas.create\_text(tx, ty, text=str(value), fill="white",
font=("Arial", 15, "bold"))

def draw\_needle(self, angle\_deg, tag): angle = math.radians(angle\_deg)
cx, cy = 260, 260 length = 160

x = cx + length \* math.cos(angle) y = cy - length \* math.sin(angle)

self.canvas.create\_line(cx, cy, x, y, fill="white", width=6, tags=tag)
self.canvas.create\_line(cx, cy, x, y, fill="#d9d9d9", width=2,
tags=tag)

def update\_gauge(self): self.canvas.delete("needle")

left\_angle = self.fuel\_to\_angle\_left(self.left\_fuel) right\_angle =
self.fuel\_to\_angle\_right(self.right\_fuel)

self.draw\_needle(left\_angle, "needle") self.draw\_needle(right\_angle,
"needle")

self.canvas.create\_oval(248, 248, 272, 272, fill="white",
outline="black", tags="needle")

rpm = self.rpm\_slider.get() fuel\_flow = self.fuel\_flow\_rate(rpm)

self.rpm\_label.config(text=f"RPM: {rpm:.0f}")
self.consumption\_label.config(text=f"Fuel Flow: {fuel\_flow:.1f}
gal/h") self.left\_label.config(text=f"Left: {self.left\_fuel:.1f}
gal") self.right\_label.config(text=f"Right: {self.right\_fuel:.1f}
gal")

def start(self): try: self.left\_fuel = float(self.left\_entry.get())
self.right\_fuel = float(self.right\_entry.get())

self.left\_fuel = min(max(self.left\_fuel, 0), MAX\_FUEL\_PER\_TANK)
self.right\_fuel = min(max(self.right\_fuel, 0), MAX\_FUEL\_PER\_TANK)

self.running = True self.last\_time = time.time() self.animate()

except ValueError: print("Please enter valid fuel values.")

def pause(self): self.running = False

def reset(self): self.running = False self.left\_fuel =
float(self.left\_entry.get()) self.right\_fuel =
float(self.right\_entry.get()) self.update\_gauge()

def animate(self): if not self.running: return

current\_time = time.time() real\_dt = current\_time - self.last\_time
self.last\_time = current\_time

simulated\_hours = real\_dt \* TIME\_SCALE / 3600

rpm = self.rpm\_slider.get() total\_flow = self.fuel\_flow\_rate(rpm)

left\_flow = total\_flow / 2 right\_flow = total\_flow / 2

self.left\_fuel -= left\_flow \* simulated\_hours self.right\_fuel -=
right\_flow \* simulated\_hours

self.left\_fuel = max(0, self.left\_fuel) self.right\_fuel = max(0,
self.right\_fuel)

if self.left\_fuel <= 0 and self.right\_fuel <= 0: self.running = False

self.update\_gauge() self.root.after(UPDATE\_MS, self.animate)

if \_\_name\_\_ == "\_\_main\_\_": root = tk.Tk() app =
FuelGaugeApp(root) root.mainloop()

