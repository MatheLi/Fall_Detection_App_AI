## The project:

It is known to medical science that falls leading to grave or even fatal injury are a great challenge that cannot be neglected, especially for elderly people. In the last few years, different kinds of approaches have been proposed in the fall detection area, which can be explained and categorized into three types: wearable device based, ambience sensor based and vision based [1]. First, wearable devices usually take advantage of embedded sensors to detect the motion and location of the body. Such mechanisms include accelerometers, magnetometers and gyroscope [2, 3]. In terms of cost, the wearable device based approach is quite low. When it comes to the installation and operation, this is not overly complicated for the elderly. Ambience based approaches, meanwhile, use pressure sensors to detect and track the body. This solution is also quite cost-effective and easy to implement [4, 5]. However, the possibility of sensing objects other than human bodies posts a remarkable challenge to the detection accuracy of this approach. Last but not least, vision based solutions make full use of deployed cameras to monitor all the objects within range, including human bodies [6, 7]. An additional advantage is that there is less intrusion into people’s daily lives than with the first two approaches. With the first two, observation space is limited and ubiquitous monitoring can’t be achieved.
 
In this project, a surveillance system based on Arduino, Windows 10 and Microsoft Azure for real-time fall detection is proposed. Raw data of three-dimensional accelerometers are provided by Arduino with ADXL345. Windows 10 devices make use of this information to get the orientation of the subject by efficient data fusion and fall detection algorithms. Microsoft Azure services and Mobile/PC application are also designed to achieve seamless data processing, analyzing, storing and acquiring any time from any place as long as they have access to the Internet. The system architecture is shown is Fig. 1 as follows. 


![](https://hackster.imgix.net/uploads/image/file/119494/Fig2.png?auto=compress%2Cformat&w=1280&h=960&fit=max)

Image source: https://hackster.imgix.net/uploads/image/file/119494/Fig2.png?auto=compress%2Cformat&w=1280&h=960&fit=max




| [More poject details](./posts/More details.md) | [Code of the first model](./posts/First_model.md) | [Code of the second model](./posts/Second_model.md) | [The team](./posts/The_team/The_team.md) |
