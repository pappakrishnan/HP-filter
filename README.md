HP Filter implementation (MATLAB)
Time-Series-Analysis

   This function decomposes a macroeconomic time series in the form of y = g + c in to a
 non-stationary trent component (g) and a stationary cyclical residual
 component (c). 
   y[t] - 1 x T matrix, g[t] - 1 x T matrix, and c[t] - 1 x T matrix.

   The output of the function will be in the form of 
                              Y = AN G,
 where Y = {y[1] y[2] y[3] y[4] ... y[T]}' (observed series), AN = T x T matrix with values of the
 elements depending on the value of the fixed parameter lambda(assumed), and 
 G = {g[1] g[2] g[3] g[4] ... g[T]}'(trend component of the series). The G
 will be the output of this function.

   The non-stationary trend or growth component need to be determined by
 minimizing the function, 

           sum (y[t] - g[t])^2 + lambda sum(g[t+1]+g[t-1]-2g[t])^2 ----(1)

   The first element is summed from t= 1 to T whereas the second element is
 summed from t= 2 to T-1.
 
   To determine the growth component (g) we use kalman filter algorithm which is stated as follows.
 
   Kalman filtering Algorithm:

       g[t] = A g[t-1]+ B u[t]+ w[t-1]     ------ (2)
       y[t] = H g[t]+ c[t]                 ------ (3)
   where w[t-1] is the process noise and c[t] is the measurement noise.
   The A, H, and B are matrices.

   Implementation of Kalman filter:
       
      Time update: (prediction)
           g'[t]= A g[t-1]+ B u[t]+ w[t-1]         - state prediction
           P'[t]= A P[t-1] A'+ Q                   - error covariance prediction
           
           where P - Covariance, Q - process noise covariance. The predicted g'[t]
           and P'[t] are passed to the measurement update section for
           corrections.

      Measurement update: (correction)
           k[t]= P'[t] H'/(H P'[t] H'+ R)           - Computing Kalman gain(k) 
           g[t]= g'[t]+ k[t] (y[t]-H g'[t])         - updating state with y[t]
           P[t]= P'[t](1-k[t] H)                    - updating error covariance. 

           where H' is the transpose of H. The calculated g[t] and P[t]
           are passed as initial values to the Time update section for the
           next iteration. The iteration is continued for T times and thereby 
           we obtain a converged series for the growth component g[t].

   Inputs:  
       y      - 1 x T matrix, where T is the number of observations.
                    
       lambda - fixed smoothening parameter. Larger the value of lamba
                smoother will be the series. Higher the frequency of the data,
                larger should to be the value of the lambda. 
                lambda = c[t]/w[t].
                Example: Quartely series ~ lambda = 1600
                         Yearly series   ~ lambda = 100
                         Monthly series  ~ lambda = 14400

   Other parameters:
                  
       g0     - Initial value for g[t] to start the iteration. As we are 
                not getting from the user, a pre-assigned value will be used.

       P0     - Initial value for P[t] to start the iteration. As we are 
                not getting from the user, a pre-assigned value will be used.  
       
   To minimize Eq (1), we get g[t+1]= 2g[t]- g[t-1]+ e where is e is the
   noise parameter. It can be written in matrix form as,
           
           {g[t+1]; g[t]}= {2 -1;1 0} {g[t]; g[t-1]}+ {e; 0}  -----(4)
   
   From (4), we get
           g[t]={g[t]; g[t-1]}
           A= {2 -1;1 0}
           w[t-1]= {e; 0}

           Also, H= {1, 0}
                 B= {0 0;0 0} (assumed)
                 P[t]={P[t] 0;0 P[t]}
                 Q={Q 0;0 0}
