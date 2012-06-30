CC=g++
CFLAGS=-c
LDFLAGS=
MTRY=2
TREES=333
SEED=31120011

test: exe xform_test rf sleep_test score
predict: exe xform_predict rf sleep_predict score

exe: bin/xform bin/rf
xform_test: workspace/xform_test
xform_predict: workspace/xform_predict
rf: workspace/rf
sleep_test:
	sleep 1
sleep_predict:
	sleep 60
score: workspace/score

bin/xform: src/xform.cpp src/xData.cpp src/xData.h
	$(CC) -I./src src/xform.cpp src/xData.cpp -o bin/xform

bin/rf: rf_src/rf.cpp rf_src/xData.cpp rf_src/xData.h rf_src/randomforest.cpp rf_src/randomforest.h
	$(CC) -I./rf_src rf_src/rf.cpp rf_src/xData.cpp rf_src/randomforest.cpp -o bin/rf

workspace/xform_predict: bin/xform
	bin/xform ~/kglSN_data/train.csv ~/kglSN_data/test.csv $(SEED) 0 0
	mv workspace/step1_test.csv .
	mv workspace/step1_test_edges.csv .
	bin/xform ~/kglSN_data/train.csv ~/kglSN_data/test.csv $(SEED) 20000 5
	mv step1_test.csv workspace/
	mv step1_test_edges.csv workspace/
	rm -f workspace/rf
	touch workspace/xform_predict

workspace/xform_test: bin/xform
	bin/xform ~/kglSN_data/train.csv ~/kglSN_data/test.csv $(SEED) 1000 5
	rm workspace/rf
	touch workspace/xform_test

workspace/rf: bin/rf
	rm -f workspace/oob_predict_*_1_$(MTRY)_$(TREES)
	rm -f workspace/tst_predict_*_1_$(MTRY)_$(TREES)
	SUBF = 1
	NUMBERS = 3 4
	#NUMBERS = 3 4 5 6 7 8 9
	$(foreach var,$(NUMBERS),./bin/rf $(var) 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv $(SUBF) > log_$(var)_1_$(MTRY)_$(TREES) &;)
	./bin/rf 2 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv $(SUBF) > log_2_1_$(MTRY)_$(TREES)
	#./bin/rf 2 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_2_1_$(MTRY)_$(TREES) &
	#./bin/rf 3 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_3_1_$(MTRY)_$(TREES) &
	#./bin/rf 4 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_4_1_$(MTRY)_$(TREES) &
	#./bin/rf 5 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_5_1_$(MTRY)_$(TREES) &
	#./bin/rf 6 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_6_1_$(MTRY)_$(TREES) &
	#./bin/rf 7 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_7_1_$(MTRY)_$(TREES) &
	#./bin/rf 8 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_8_1_$(MTRY)_$(TREES) &
	#./bin/rf 9 1 $(MTRY) $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_9_1_$(MTRY)_$(TREES) 
	rm -f workspace/score
	touch workspace/rf

workspace/score: workspace/rf
	./merge.pl workspace/ _ _1_$(MTRY)_$(TREES)
	./score.pl workspace/oob_predict_merge.csv workspace/tst_predict_merge.csv
	touch workspace/score

clean:
	rm -f workspace/oob_predict*
	rm -f workspace/tst_predict*
	rm -f log*
