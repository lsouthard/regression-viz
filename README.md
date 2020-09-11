# regression-viz

You can run your regression analysis (any variation) and then apply the following code: 

 
```
model.glm = glm(as.numeric(outcome == "Y") ~ Group + var1 + var2 + Group*var2, 
                                            family = **specific to your model**, 
                                            data = model.df) 

results <-  as.data.frame(get_model_data(model.glm, type = "eff", terms = c("Group"))) %>%              
            mutate(Group = if_else(x == 1, "Group 1", "Group 2"), 
                   predicted = (predicted*100), 
                   conf.low = (conf.low*100), 
                   conf.high = (conf.high*100)) %>%  
            dplyr::select("Group", "pp" = "predicted", "std.error", "conf.low", "conf.high")
```
Review the different type = … but type = "eff" means that you don't have to use a true reference group, but instead you can use a proportion of your data. My goal in the code above was to get predictive probabilities which allows a simple way to explain regression effects. get_model_data comes from library(ggeffects).  I like this method when I'm using ethnicity or or any other time where there's potential for the results to be biased. 

Then you can visualize it using this code: 

```
ggplot(results, aes(x= Group, y = pp))+ 
  geom_errorbar(aes(ymin=conf.low, ymax=conf.high), width=.1) + 
  geom_point(size = 3)+ 
  labs(y = "% Predicted Probability of Returning", x = "Group")+ 
  scale_y_continuous(limits = c(0, 100), expand = c(0,0))+ 
  theme(axis.line = element_line(color = "black"), 
        axis.title.y = element_text(size =20),  
        axis.title.x = element_text(size= 20), 
        axis.text.x = element_text(color = "black", size = 16), 
        axis.text.y = element_text(size = 16, color = "black"), 
        panel.background = element_blank(), 
        panel.grid.major=element_line("gray"), 
        panel.grid.minor=element_line("gray"), 
        text=element_text(size = 18, family = "serif") 
  ) 
```
Here is what the code above will produce:

![ProbViz](https://user-images.githubusercontent.com/51967620/92968730-e543e780-f438-11ea-8350-c7199245ecd1.png)

The benefit of visualizing regression coefficients this way is simplicity. We can explain that if the error bars overlap than the effect is nonsignificant, but since they don't here, it is significant. Also the outcome is higher for the dot that's higher up on the plot. This particular example leverages predicted probabilities, but there are other options in the code.  
