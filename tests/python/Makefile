EQY := eqy
PYTHON_TEMPDIR := $(shell python3 -c "import tempfile; print(tempfile.gettempdir())")

test: failing_tests passing_tests

passing_tests:
	@$(EQY) -f splitnets.eqy > /dev/null
	@$(EQY) --init-config-file generate.eqy counter counter.sv counter.sv 2>&1 | grep -i "template config written to"
	@rm -rf counter/ counter.bak*/; $(EQY) counter.eqy > /dev/null
	@$(EQY) -b counter.eqy > /dev/null # 1st counter
	@$(EQY) -b counter.eqy > /dev/null # 2nd counter
	@$(EQY) -t counter.eqy | grep -i "make -C $(PYTHON_TEMPDIR)" # Use temp
	@rm -rf counter/ counter.bak*/; $(EQY) counter.eqy -d counter | grep -i "make -C counter -f strategies.mk"   # setting workdir
	@$(EQY) -c counter.eqy --purge "counter.state/simple" 2>&1 | grep -i "Removing" # continue with purge
	@$(EQY) -f counter_cells.eqy 2>&1 | grep -i "counter.state__SDFF_PP0__Q" # matching cells
	@$(EQY) - < counter.eqy 2>&1 | grep -i "Cannot derive workdir name from config file name" # stdin
	@$(EQY) -f counter.eqy --setup | grep -i "DONE (UNKNOWN, rc=0)" # setup only
	@$(EQY) -f failed_partition.eqy > /dev/null

failing_tests:
	@$(EQY) 2>&1 | grep -i "No config file given"
	@$(EQY) -f dupl_section.eqy 2>&1 | grep -i "duplicated strategy section"
	@$(EQY) -f error_verilog.eqy 2>&1 | grep -i "Reading sources failed"
	@$(EQY) -f read_source_fail.eqy --yosys /tmp/yosys 2>&1 | grep -i "Reading sources failed"
	@timeout 2s $(EQY) -f timeout.eqy 2>&1 | grep -i "Keyboard interrupt or external termination signal"
	@$(EQY) counter.eqy -t -d counter 2>&1 | grep -i "Cannot use -d with -t"
	@mkdir -p counter; $(EQY) counter.eqy 2>&1 | grep -i "Directory 'counter' already exists"
	@$(EQY) counter.eqy -d counter 2>&1 | grep -i "Directory 'counter' already exists" # setting workdir when exist
	@$(EQY) -f counter.eqy -p stat_error counter.state 2>&1 | grep -i "returned a non-zero exit code" # non-working command
	@$(EQY) -c counter.eqy -d counter.eqy 2>&1 | grep -i "Cannot continue in 'counter.eqy': not a directory"
	@rm -rf counter/; $(EQY) -c counter.eqy 2>&1 | grep -i "Cannot continue in" # Cannot continue
	@$(EQY) --debug -f picorv32_vivado.eqy 2>&1 | grep -i "DONE (FAIL, rc=2)"
	@$(EQY) -f syntax_error.eqy 2>&1 | grep -i "syntax error in syntax_error.eqy line 16"
	@$(EQY) -f syntax_error_options.eqy 2>&1 | grep -i "syntax error in options section"
	@$(EQY) -f syntax_error_partition.eqy 2>&1 | grep -i "Syntax error in partition command"
	@$(EQY) -f syntax_error_collect.eqy 2>&1 | grep -i "Syntax error in collect command"
	@$(EQY) -f unknown_option.eqy 2>&1 | grep -i "unknown option"
	@$(EQY) -f unknown_strategy.eqy 2>&1 | grep -i "Unknown strategy type"
	@$(EQY) -f expected_opt_bool_val.eqy 2>&1 | grep -i "expected option value"
	@$(EQY) -f expected_opt_str_val.eqy 2>&1 | grep -i "expected option value"
	@$(EQY) -f expected_opt_int_val.eqy 2>&1 | grep -i "expected option value"
	@$(EQY) -f repeated_option_bool.eqy 2>&1 | grep -i "repeated option"
	@$(EQY) -f repeated_option_str.eqy 2>&1 | grep -i "repeated option"
	@$(EQY) -f repeated_option_int.eqy 2>&1 | grep -i "repeated option"
	@$(EQY) -f repeated_option_use.eqy 2>&1 | grep -i "repeated option"
	@$(EQY) -f no_use_line.eqy 2>&1 | grep -i "has no 'use' line"
	@$(EQY) -f expected_opt_bool_on_off.eqy 2>&1 | grep -i "expected one of"
	@$(EQY) -f expected_opt_int_integer.eqy 2>&1 | grep -i "expected integer option"
	@$(EQY) -f unknown_opt_other.eqy 2>&1 | grep -i "unknown option 'unknown'"
	@$(EQY) -f equivalence_problem.eqy 2>&1 | grep -i "A problem occurred during equivalence check."
	@$(EQY) -f problem_occured.eqy 2>&1 | grep -i "No configured strategy supports partition"
	@$(EQY) -f missing_gate_section.eqy 2>&1 | grep -i "section \[gate\] missing"
	@$(EQY) -f missing_gold_section.eqy 2>&1 | grep -i "section \[gold\] missing"

clean:
	@rm -rf counter/ counter.bak*/ counter_cells/ error_verilog/ generate.eqy picorv32_vivado/ read_source_fail/ timeout/ splitnets/ problem_occured/ failed_partition/ equivalence_problem/ syntax_error_partition/ syntax_error_collect/

.PHONY: test clean
