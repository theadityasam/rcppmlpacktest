# Medium Test Results
I'll be including support for dbscan implemented in mlpack and will be incorporating it in R by providing an R wrapper function. I've added it in the package in my own fork of RcppMLPACK2
## Add dbscan.cpp
```
#include <RcppMLPACK.h>				// MLPACK, Rcpp and RcppArmadillo
#include <mlpack/methods/dbscan/dbscan.hpp> 	// particular algorithm used here
//' Run a dbscan clustering analysis
//'
//' DBSCAN clustering on the data, returning number of clusters, 
//' the centroid of each cluster and also the list of cluster assignments.
//'
//' @title Run a DBSCAN clustering analysis
//' @param data A matrix of data values
//' @return assignments	Vector to store cluster assignments
//' @return centroids Matrix in which centroids are stored
//' @examples
//' x <- rbind(matrix(rnorm(100, sd = 0.3), ncol = 2),
//'            matrix(rnorm(100, mean = 1, sd = 0.3), ncol = 2))
//' colnames(x) <- c("x", "y")
//' cl <- dbscan(x)
//'
//' data(trees, package="datasets")
//' cl2 <- dbscan(t(trees))
// [[Rcpp::export]]
Rcpp::List dbscan(const arma::mat& data) {
    
    arma::Row<size_t> assignments; 		// to store results
	arma::mat centroids;
    mlpack::dbscan::DBSCAN<> dbs;    		// initialize with the default arguments.
    
    dbs.Cluster(data, &assignments, &centroids);     // make call, filling 'assignments'

    return Rcpp::List::create(Rcpp::Named("Result") = assignments,
    						  Rcpp::Named("Centroids") = centroids);
}
```
## Editing  RcppExports.cpp in src
```
// dbscan
Rcpp::List dbscan(const arma::mat& data);
RcppExport SEXP _RcppMLPACK_dbscan(SEXP dataSEXP) {
BEGIN_RCPP
    Rcpp::RObject rcpp_result_gen;
    Rcpp::RNGScope rcpp_rngScope_gen;
    Rcpp::traits::input_parameter< const arma::mat& >::type data(dataSEXP);
    rcpp_result_gen = Rcpp::wrap(dbscan(data));
    return rcpp_result_gen;
END_RCPP
}

static const R_CallMethodDef CallEntries[] = {
    {"_RcppMLPACK_coverTreeNeighbor", (DL_FUNC) &_RcppMLPACK_coverTreeNeighbor, 2},
    {"_RcppMLPACK_kMeans", (DL_FUNC) &_RcppMLPACK_kMeans, 2},
    {"_RcppMLPACK_LARS", (DL_FUNC) &_RcppMLPACK_LARS, 6},
    {"_RcppMLPACK_linearRegression", (DL_FUNC) &_RcppMLPACK_linearRegression, 4},
    {"_RcppMLPACK_logisticRegression", (DL_FUNC) &_RcppMLPACK_logisticRegression, 3},
    {"_RcppMLPACK_naiveBayesClassifier", (DL_FUNC) &_RcppMLPACK_naiveBayesClassifier, 4},
    {"_RcppMLPACK_dbscan", (DL_FUNC) &_RcppMLPACK_dbscan, 1},
    {NULL, NULL, 0}
};
```
## Adding the dbscan call method in RcppExports.R
```
#' Run a dbscan clustering analysis
#'
#' DBSCAN clustering on the data, returning number of clusters, 
#' the centroid of each cluster and also the list of cluster assignments.
#'
#' @title Run a DBSCAN clustering analysis
#' @param data A matrix of data values
#' @return assignments	Vector to store cluster assignments
#' @return centroids Matrix in which centroids are stored
#' @examples
#' x <- rbind(matrix(rnorm(100, sd = 0.3), ncol = 2),
#'            matrix(rnorm(100, mean = 1, sd = 0.3), ncol = 2))
#' colnames(x) <- c("x", "y")
#' cl <- dbscan(x)
#'
#' data(trees, package="datasets")
#' cl2 <- dbscan(t(trees))
dbscan <- function(data) {
    .Call(`_RcppMLPACK_dbscan`, data)
}
```
Hence, the dbscan() function will access the dbscan method implemented in the mlpack source
