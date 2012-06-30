CC=g++
CFLAGS=-c
LDFLAGS=
TREES=125
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
	bin/xform ../data/train.csv ../data/test.csv $(SEED) 0 0
	mv workspace/step1_test.csv .
	mv workspace/step1_test_edges.csv .
	bin/xform ../data/train.csv ../data/test.csv $(SEED) 20000 5
	mv step1_test.csv workspace/
	mv step1_test_edges.csv workspace/
	touch workspace/xform_predict

workspace/xform_test: bin/xform
	bin/xform ../data/train.csv ../data/test.csv $(SEED) 1000 5
	touch workspace/xform_test

workspace/rf: bin/rf
	rm -f workspace/oob_predict_*_1_1_$(TREES)
	rm -f workspace/tst_predict_*_1_1_$(TREES)
	./bin/rf 2 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_2_1_1_$(TREES) &
	./bin/rf 3 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_3_1_1_$(TREES) &
	./bin/rf 4 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_4_1_1_$(TREES) &
	./bin/rf 5 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_5_1_1_$(TREES) &
	./bin/rf 6 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_6_1_1_$(TREES) &
	./bin/rf 7 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_7_1_1_$(TREES) &
	./bin/rf 8 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_8_1_1_$(TREES) &
	./bin/rf 9 1 1 $(TREES) workspace/step1_train.csv workspace/step1_test.csv 1 > log_9_1_1_$(TREES) 
	touch workspace/rf

workspace/score: workspace/rf
	./merge.pl workspace/ _ _1_1_$(TREES)
	./score.pl workspace/oob_predict_merge.csv workspace/tst_predict_merge.csv
	touch workspace/score

clean:
	rm -f workspace/oob_predict*
	rm -f workspace/tst_predict*
	rm -f log*
