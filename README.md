# io_buttons

Left, middle, and right are used to focus on out, b, and a respectively. We focus because
we only have 32 lights to work with and our alu is 32 bits. When we focus on a, all 32 lights
will be dedicated to representing a in binary form. The same applies for b and a.

The left button serves one more function of toggling between focusing on out and misc
information. We still need to display information about alufn and other things like z, v, and
n, so they fall under the misc. When focusing on misc, led[2:0] will display z, v, and n in
that order with z at led[0]. io_led[0][5:0] will display alufn. io_led[1] and io_led[2] are
reserved for displaying test information.

The top and bottom buttons are to add or subtract the focused a or b. They won't do anything
when misc and out are focused. How much to add or subtract is dependent on the value delta
which will be set by io_dip[1] and io_dip[2]. In other words, delta is a 16 bit signed
number. if delta is 1 and we click the bottom button, a will become a - 1. We can force
it to continuously add or subtract without pressing the button repeatedly by ensuring
io_dip[0][6] is set to 1 before we add or subtract. The FGPA will continuously add or subtract
and we can exit that mode by simply setting io_dip[0][6] to 0.

# io_dips

io_dip[2:1] are used to set the value of delta which will be used by the io_buttons.

io_dip[0][5:0] is used to set alufn

io_dip[0][6] is reserved for automatic adding/subtraction with delta

io_dip[0][7] is used to initiate testing by the FSM. During which the alu is taken over
by the FSM tester and user inputs wont matter except reversing io_dip[0][7] which is
used to simulate an error case.

# io_segments

Shows the value of whatever is in focus. If misc or out is in focus then out will always be
displayed. The segments work for values from -512 to 511 (both inclusive). For more accurate
readings please use io_led and led.

# io_led and led

These work in conjunction to show the binary representation of the focused value.

When focused on misc, led[0] = z, led[1] = v, and led[2] = n. io_led[0] = alufn, io_led[1] =
current test or failed test or last test in binary form with the first test at 0, io_led[2][0]
= testing completed, io_led[2][1] = testing failed, io_led[2][3:2] = testing dff state with 0
being not yet, 1 being testing, and 2 being completed.

When focused on a, b, or out, led will show the 8 least significant bits while io_led will
show the other 24 bits with the least significant of the 24 starting from io_led[0][0]. led[0]
shows the least significant bit.

# testing

Set io_dip[0][7] to one and the FSM tester will initiate tests. Focus on misc to reveal
test information. The last test's a, b, out, and all misc information will be saved. Simply
press reset to purge all saved values when test completes. The saved values can be read by
selecting the focus with relevant io_buttons.
