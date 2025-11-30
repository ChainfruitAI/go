#6 - R PROGRAMMING IRIS DATASET


install.packages("ggplot2")
install.packages("caret")

library(ggplot2)
library(caret) 


data(iris)
head(iris, n = 10) 

qplot(Sepal.Length, Petal.Length, data = iris)
qplot(Sepal.Length, Petal.Length, data = iris, color = Species)
qplot(Sepal.Length, Petal.Length, data = iris, color = Species, size = Petal.Width)
qplot(Sepal.Length, Petal.Length, 
      data = iris, 
      color = Species, 
      size = Petal.Width, 
      alpha = I(0.7))
qplot(Sepal.Length, Petal.Length, 
      data = iris, 
      color = Species,
      xlab = "Sepal Length",
      ylab = "Petal Length", 
      main = "Sepal vs. Petal Length in Iris data")

par(mfrow = c(1, 4))
for(i in 1:4) {
  hist(iris[, i], main = names(iris)[i], xlab = "Value", col = "skyblue")
}
par(mfrow = c(1, 1))

pairs(iris, pch = 19, col = iris$Species)


featurePlot(x = iris[, 1:4],
            y = iris[, 5],
            plot = "density",
            scales = list(
              x = list(relation = "free"),
              y = list(relation = "free")
            ), 
            auto.key = list(columns = 3))
