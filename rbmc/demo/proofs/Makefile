PROJECT=gotoc-static-mutable-struct
SRCDIR=$(abspath ../src)
PROOFDIR=$(abspath .)

RUSTC=rustc
VIEWER=cbmc-viewer

RESULT=result.xml
PROPERTY=property.xml
COVERAGE=coverage.xml

$(PROJECT).json: $(SRCDIR)/$(PROJECT).rs
	$(RUSTC) -Z codegen-backend=gotoc $^ --out-dir .

$(PROJECT)1.goto: $(PROJECT).json
	symtab2gb $^ --out $@

$(PROJECT).goto: $(PROJECT)1.goto
	goto-cc --function main $^ -o $@

$(RESULT): $(PROJECT).goto
	- cbmc --object-bits 11 --unwind 10 --pointer-check --bounds-check $^ \
		--trace \
		--xml-ui > $@

$(PROPERTY): $(PROJECT).goto
	cbmc --object-bits 11 --unwind 10 --pointer-check --bounds-check $^ \
		--show-properties \
		--xml-ui > $@

$(COVERAGE): $(PROJECT).goto
	cbmc --object-bits 11 --unwind 10 --pointer-check --bounds-check $^ \
		--cover location \
		--xml-ui > $@

report: $(RESULT) $(PROPERTY) $(COVERAGE)
	$(VIEWER) \
		--result $(RESULT) \
		--coverage $(COVERAGE) \
		--property $(PROPERTY) \
		--srcdir $(SRCDIR) \
		--goto $(PROJECT).goto \
		--reportdir $(PROOFDIR)/report


clean:
	$(RM) *~ .*~ \#* .\#*
	$(RM) $(PROJECT).json
	$(RM) $(PROJECT)1.goto
	$(RM) $(PROJECT).goto
	$(RM) $(RESULT)
	$(RM) $(PROPERTY)
	$(RM) $(COVERAGE)
	$(RM) -r $(PROOFDIR)/report

.PHONY: clean report
