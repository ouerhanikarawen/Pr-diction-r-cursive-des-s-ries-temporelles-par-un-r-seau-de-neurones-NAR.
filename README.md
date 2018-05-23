N = 1200;
Nu = 600;
b = 0.1;
c = 0.2;
tau = 17;
y = [1 1.1 1.2 1.3 1.4 1.5 1.6 1.7 1.8 1.9 2 2.1 2.2 2.3 2.4 2.5 2.6 2.7]';
for n=18:N+99
 y(n+1) = y(n) - b*y(n) + c*y(n-tau)/(1+y(n-tau).^10);
end
y(1:100) = [];
plot(y,'r')
grid on, hold on
plot(y(1:Nu),'b')
legend('données de validation','données de formation')
title('A Mackay-Glass time serie')
xlabel('t')
ylabel('x(t)')
ylim([-0.6 1.6])
set(gcf,'position',[1 60 800 400])
yt = con2seq(y(1:Nu)');
yv = con2seq(y(Nu+1:end)');
feedbackDelays = 1:6:19;
hiddenSizes = [6 3];
Na=narnet(feedbackDelays,hiddenSizes)
[Xs,Xi,Ai,Ts] = preparets(Na,{},{},yt);
Na = train(Na,Xs,Ts,Xi,Ai);
view(Na)
Na= closeloop(Na);
view(Na);
yini = yt(end-max(feedbackDelays)+1:end);
[Xs,Xi,Ai] = preparets(Na,{},{},[yini yv]);
predict = Na(Xs,Xi,Ai);
Yv = cell2mat(yv);
Yp = cell2mat(predict);
e = Yv - Yp;
figure(1)
plot(Nu+1:N,Yp,'m')
plot(Nu+1:N,e,'g')
legend('données de validation','données de formation','prédiction','erreur')
