
loading a binary as a project
```
import angr

proj = angr.Project('<binary path>')
```

create a factory and state from the project
```
state = proj.factory.blank_state(addr = <random address>)
```

can see the register input at the address
```
state.regs.eax
```

step in angr -> 1 step in symbolic execution 
```
s = state.step()
```

or 
```
new_state = s.successors[0]
new_state
```

## State Options
how you set up your Angr state affects its performance

```
angr.options.LAZY_SOLVES
```
don't check satisfiability until absolutely necessary 
enabling it made performance faster

```
angr.options.simplification
```
cause data to run through z3's simplifiers before it reaches memory or register storage
disabling it made performance faster


## Symbolic and non-symbolic variables in Angr
BVV -> non-symbolic sequence of bits (e.g. a)
BVS -> symbolic sequence of bits (e.g. b)

```
import claripy

a = claripy.BVV(3)
a.concrete //returns true
a.concrete_value

b = claripy.BVS('var_b', 32)
b.concrete //returns false
```


## Scenario

Lets say we want an input value that makes this function return 1

function signature
```
int secret_func(int input) {

}
```

```
import claripy 
inta = claripy.BVS('a', 32)


state = proj.factory.call_state(0x401176, inta) //address of the 

state
```

call_state params -> address of the function + arguments of the function 

execution is handled by the simulation manager
```
simgr = proj.factory.simulation_manager(state)

simgr.explore(find=<address of instruction you want to land on>, avoid=<avoid address)

simgr
```
find the address of the instructions you want to avoid too 
find the address of an instruction that you want to land on
can ignore the errors for now

```
simgr.found
simgr.avoid
simgr.avoid[0].solver.eval(inta)
simgr.found[0].solver.eval(inta)
```


can add multiple avoid address
```
simgr.explore(find=addr, avoid=[addr1, addr2])
```

gdb-multiarch -> to run arm binary with gdb debugger

## how to hook functions in angr
scanf and printf from the function might result in longer run times. hook them and replace them instead
```
class my_scanf(angr.SimProcedure):
	def run(self, format_string, int_addr):
		print("scanf hook called!")
		inta = claripy.BVS('a', 32)
		self.state.memory.store(int_addr, inta, endness=project.arch.memory_endness)
		self.state.globals['inta'] = inta

project.hook_symbol('__isoc99_scanf', my_scanf(), replace=True)
project.hook_symbol('printf', angr.SIM_PROCEDURES['libc']['printf'](), replace=True)
```

