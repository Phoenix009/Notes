
- `Runner`:
	- Base Class for testing inputs
	- Defines the test outcomes, `PASS`, `FAIL`, `UNRESOLVED`
	- `def run(inp:str)`: runs the runner with the given input

- `PrintRunner`:
	- Simple runner, printing the input

- `ProgramRunner`:
	- Test a program with inputs
	- `self.program` is a program spec as passed to `subprocess.run()`
	- `run_process(inp)`: Runs the program with `inp` as input
	- 