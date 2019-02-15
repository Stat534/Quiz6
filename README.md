# Quiz6

### Question 1 (4 points)
Describe the process of fitting spatial covariance parameters using an empirical variogram. How does this change if we anticipate covariate explaining some of the mean structure?

### Question 2 (4 points)
Recap the differences between point referenced data and point process or areal data. Then describe general research questions that can be asked and answered with point referenced data.

### Question 3. (2 points)

In class on Monday we will use [JAGS](http://mcmc-jags.sourceforge.net) through R, but JAGS needs to be installed.
[Download JAGS](https://sourceforge.net/projects/mcmc-jags/files/) to verify JAGS is properly installed. Run the following R code (note we will discuss this in class on Tuesday)

```{r}
# Simulate Data
library(rjags)
library(dplyr)
set.seed(02142019)
alpha.true <- 0
beta.true <- 1
sigma.true <- 1
num.pts <- 100

x <- runif(num.pts,0,10)
y <- rnorm(num.pts, mean = rep(alpha.true, num.pts) + x*beta.true, sd = sigma.true)

# Specify data for JAGS           
data.in <- list(x = x, y = y, N = num.pts)

#Define Model
model_string <- "model{
  # Likelihood
  for(i in 1:N){
    y[i]   ~ dnorm(mu[i], tau)
    mu[i] <- alpha + beta * x[i]
  }

  # Priors
  sigma <- 1/sqrt(tau)
  alpha ~ dnorm(0, 1.0E-6)
  beta ~ dnorm(0, 1.0E-6)
  tau ~ dgamma(1E-6, 1E-6)
}"

# compile model
model <- jags.model(textConnection(model_string), data = data.in)

# burn in
update(model, 10000)

# draw samples
samp <- coda.samples(model, 
        variable.names=c("alpha","beta","sigma"), 
        n.iter=20000)

# plot samples
summary(samp)
```
