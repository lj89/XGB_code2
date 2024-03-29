# XGB_code2

#xgb code

from sklearn.model_selection import train_test_split
from sklearn.model_selection import KFold
from sklearn.metrics import roc_auc_score
from sklearn.ensemble import RandomForestClassifier
import lightgbm as lgb
import xgboost as xgb

# Random Forest Parameters
RFC_METRIC = 'gini'
NUM_ESTIMATORS = 100
NO_JOBS = 4

#Train, Test, Validation datasets
VALID_SIZE  = 0.2
NUMBER_KFOLDS = 5
RANDOM_STATE = 2000

#LightGBM, XGBOOST
MAX_ROUNDS = 1000
EARLY_STOP = 50
OPT_ROUNDS = 1000
VERBOSE_EVAL = 50


# XGBoost Parameters
params = {}
#params['objective'] = 'multi:softprob'  #'binary:logistic'
params['objective'] = 'multi:softmax'
#params['objective'] = 'reg:linear'
#self.n_classes_
params['eta'] = 0.039
params['silent'] = True
params['max_depth'] = 6
params['subsample'] = 0.8
params['colsample_bytree'] = 0.9
params['eval_metric'] = 'auc'
params['random_state'] = RANDOM_STATE

watchlist = [(dtrain, 'train'), (dvalid, 'valid')]

##########################
#Prediction
target = 'Class'
predictors = ['Time', 'V1', 'V2', 'V3', 'V4', 'V5', 'V6', 'V7', 'V8', 'V9', 'V10','V11', 'V12', 'V13', 'V14', 'V15', 'V16', 'V17', 'V18', 'V19',
       'V20', 'V21', 'V22', 'V23', 'V24', 'V25', 'V26', 'V27', 'V28', 'Amount']

#Train, Validation
train_df, val_df = train_test_split(data_df, test_size = VALID_SIZE, random_state = RANDOM_STATE, shuffle = True)



xgb_clf = xgb.train(params,
                   dtrain,
                   MAX_ROUNDS,
                   watchlist,
                   early_stopping_rounds = EARLY_STOP,
                   maximize = True,
                   verbose_eval = VERBOSE_EVAL)



####################
preds = xgb_clf.predict(dvalid)
print('XGBoost - roc_auc_score: ', roc_auc_score(val_df[target].values, preds))


####################
fig, ax = plt.subplots(ncols = 1, figsize = (8, 5))
xgb.plot_importance(xgb_clf, height = 0.5, title = 'Feature Importance - XGBoost', ax = ax, color = 'green')
plt.show()
