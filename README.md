# ai-project5
## Bai 1:
```php
        self.features = trainingData[0].keys() # could be useful later
        # DO NOT ZERO OUT YOUR WEIGHTS BEFORE STARTING TRAINING, OR
        # THE AUTOGRADER WILL LIKELY DEDUCT POINTS.

        for iteration in range(self.max_iterations):
            print "Starting iteration ", iteration, "..."
            for i in range(len(trainingData)):
                "*** YOUR CODE HERE ***"
                #util.raiseNotDefined()

                scores = util.Counter()
                scores.update({L: self.weights[L] * trainingData[i] for L in self.legalLabels})
                label = scores.argMax()
                    
                if label != trainingLabels[i]:
                    self.weights[trainingLabels[i]] += trainingData[i]
                    self.weights[label] -= trainingData[i]
 ```                   
### Áp dụng công thức đề bài cho

## Bài 2
```php
    def findHighWeightFeatures(self, label):
        """
        Returns a list of the 100 features with the greatest weight for some label
        """
        featuresWeights = []

        "*** YOUR CODE HERE ***"
        #util.raiseNotDefined()
        featuresWeights = self.weights[label].sortedKeys()

        weights = []
        for i in range(100):
            weights.append(featuresWeights[i])
        
        return weights

```
### Sử dụng hàm sortKey của datastructer Counter để xếp weights theo thứ tự giảm dần rồi trả về list có 100 phần tử

## Bài 3
```php
    def trainAndTune(self, trainingData, trainingLabels, validationData, validationLabels, Cgrid):
        """
        This method sets self.weights using MIRA.  Train the classifier for each value of C in Cgrid,
        then store the weights that give the best accuracy on the validationData.

        Use the provided self.weights[label] data structure so that
        the classify method works correctly. Also, recall that a
        datum is a counter from features to values for those features
        representing a vector of values.
        """
        "*** YOUR CODE HERE ***"
        #util.raiseNotDefined()

        best_tuned_weights = {}
        best_classification_accuracy = None

        for c_value in Cgrid:
            local_weights = self.weights.copy()
            local_classification_accuracy = 0

            for iteration in range(self.max_iterations):
                local_iteration_classification_accuracy = 0

                print "Starting iteration ", iteration, "..."
                for i in range(len(trainingData)):

                    training_data_instance = trainingData[i]
                    training_data_label = trainingLabels[i]

                    # When we come to an instance (f,y)(f,y), we find the label with highest score
                    (max_label_score, best_label) = (None, None)
                    for label in self.legalLabels:
                        local_label_score = 0

                        for feature in self.features:
                            local_label_score += local_weights[label][feature] * training_data_instance[feature]
                        
                        if max_label_score is None or max_label_score < local_label_score:
                            max_label_score = local_label_score
                            best_label = label

                    # adjust weights if arg-max label is different than training_data_label
                    if best_label != training_data_label:

                        # MIRA : choose	an update size that fixes the current mistake but, minimizes the change	to w
                        tuning_rate = min(c_value, ((local_weights[best_label] - local_weights[training_data_label]) *
                                                    training_data_instance + 1.0) / (2.0 * (training_data_instance *
                                                                                            training_data_instance)))

                        # update the weights with the tuning (learning) rate value.
                        for feature in self.features:
                            local_weights[best_label][feature] -= tuning_rate * training_data_instance[feature]
                            local_weights[training_data_label][feature] += tuning_rate * training_data_instance[feature]
                    else:
                        local_iteration_classification_accuracy += 1

                # update the local c-value classification accuracy.
                if local_iteration_classification_accuracy > local_classification_accuracy:
                    local_classification_accuracy = local_iteration_classification_accuracy

            if local_classification_accuracy > best_classification_accuracy:
                best_classification_accuracy = local_classification_accuracy
                best_tuned_weights = local_weights

        self.weights = best_tuned_weights
   ```
   ## cách làm
    Loop trên CGrid
    Làm tưởng tự với câu 1
    Phần cập nhật: theo phần mô tả của tài liệu
    Check accuracy:
    2 vòng loop lồng nhau trên validationData -> validationLabels
    Tìm labelMax -> Tính tổng số labelMax = validationLabels[i]
    Cập nhật weights = gridWeights[Cmax]
