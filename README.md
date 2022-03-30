# Forward-Backward-selection

#forward selection
from sklearn.linear_model import LinearRegression

best_selected_features = []
best_features = N
rss_scores = {}

for i in range(best_features):
    
    least_rss_score = np.inf
    least_rss_feature = None
    
    for feature in data.columns.difference(best_selected_features): 
        linreg = LinearRegression()
        using_features = best_selected_features + [feature]
        linreg.fit(data_train[using_featires].values, y.to_numpy()) #data is independent variables, y is dependent variable
        prediction = linreg.predict(data_test[using_features].values)
        prediction_rss = RSS(prediction, y_test.to_numpy())
        
        if least_rss_score > prediction_rss:
            least_rss_score = prediction_rss
            least_rss_feature = feature

    best_selected_features.append(least_rss_feature)
    rss_scores[len(best_selected_features)] = least_rss_score
    
    #backward selection
    
all_features = list(data.columns)
current_selected_features = all_features[:]
removed_features = []
number_of_best_features = 5
rss_scores = {}


def get_test_rss(features, data_train, y_train, data_test, y_test):
    lin_reg = LinearRegression()
    lin_reg.fit(data_train[features].values, y_train.to_numpy())
    predictions = lin_reg.predict(data_test[features].values) 
    return RSS(predictions, y_test.to_numpy())


while len(current_selected_features) != number_of_best_features:
    rss_scores[len(current_selected_features)] = get_test_rss(current_selected_features, data_train, y_train, data_test, y_test)
    X2 = sm.add_constant(data_train[current_selected_features].values)
    est_ = sm.OLS(y_train, X2)
    est2 = est_.fit()
    # print(est2.summary())
    p_values = est2.summary2().tables[1]['P>|t|'][1:]
    highest_p_feature = p_values.argmax()
    current_selected_features.pop(highest_p_feature)
    
rss_scores[len(current_selected_features)] = get_test_rss(current_selected_features, data_train, y_train, data_test, y_test)
