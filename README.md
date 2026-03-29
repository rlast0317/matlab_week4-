clear; clc; close all;

%% 1. 근과 최소값 계산 및 전역 최소 증명
% 함수 핸들을 생성하여 .m 파일을 호출함
f_1 = @(x) kms_24013976_func_1(x);

% (1) 근 구하기 (fzero 사용, 초기값 0과 1.5)
root1 = fzero(f_1, 0);   
root2 = fzero(f_1, 1.5); 

% (2) 최소값 구하기 (fminbnd 사용, 범위 0~4)
[x_min, f_min] = fminbnd(f_1, 0, 4);

% 결과 출력
fprintf('[문제 1 결과]\n');
fprintf('근: x = %.4f, x = %.4f\n', root1, root2);
fprintf('최소값 위치: x = %.4f, 최소값: f(x) = %.4f\n\n', x_min, f_min);

% (3) 전역 최소값 증명 그래프
x_val = linspace(0, 4, 1000); 
figure; 
plot(x_val, f_1(x_val), 'b-', 'LineWidth', 1.5); hold on;
% 근(Roots) 표시 (검은색 네모)
plot([root1, root2], [0, 0], 'ks', 'MarkerSize', 8, 'MarkerFaceColor', 'k'); 
% 최소값(Minimum) 표시 (빨간색 원)
plot(x_min, f_min, 'ro', 'MarkerSize', 8, 'MarkerFaceColor', 'r'); 
grid on; 
title('문제 1: f(x) = 0.3x - sin(2x)'); 
xlabel('x'); ylabel('f(x)');
legend('f(x)', '근(Roots)', '전역 최소값', 'Location', 'best'); 
hold off;

% 증명 텍스트 출력
disp('-> 증명: 그래프 확인 결과, 닫힌 구간 [0, 4] 내에서 구한 최소값(빨간 점)이 가장 낮은 위치에 있으므로 전역 최소임이 증명됨.');


%% 2. 수직으로 던져진 물체의 도달 시간
% 주어진 조건
h_target = 100; % [m]
v0 = 50;        % [m/s]
g = 9.81;       % [m/s^2]

% 함수 호출
[t_up, t_down] = kms_24013976_func_2(h_target, v0, g);

% 결과 출력
fprintf('\n[문제 2]\n');
fprintf('초기속도 %.0fm/s로 던져진 물체가 %.0fm 높이에 도달하는 시간:\n', v0, h_target);
fprintf('t1 = %.4f 초\n', t_up);
fprintf('t2 = %.4f 초\n\n', t_down);

% 답의 해석
disp('<두 개의 답 해석>');
disp('t1 (짧은 시간): 물체가 위로 솟구치며 처음으로 100m 높이를 통과할 때 걸리는 시간입니다.');
disp('t2 (긴 시간): 물체가 최고점을 찍고 다시 자유낙하하면서 두 번째로 100m 높이를 통과할 때 걸리는 시간입니다.');




%% 3. 원뿔 종이컵의 체적 및 표면적 최적화
disp('=== 문제 3 ===');

% --------------------------------------------------
% [ (a)번 풀이: 수식 유도 ]
% --------------------------------------------------
disp('[(a) h를 소거한 A에 대한 식 유도]');
disp('1. 체적 V = (1/3)*pi*r^2*h 에서 높이 h에 대해 정리하면 h = 3V / (pi*r^2) 입니다.');
disp('2. 이를 표면적 A = pi*r*sqrt(r^2 + h^2) 에 대입하여 h를 소거합니다.');
disp('3. 식을 정리하면 최종적으로 A(r) = sqrt(pi^2 * r^4 + 9 * V^2 / r^2) 가 도출됩니다.');
disp(' ');

% --------------------------------------------------
% [ (b)번 풀이: 전역 변수 선언 및 함수 안내 ]
% --------------------------------------------------
disp('[(b) 전역 변수 선언 및 사용자 정의 함수]');
disp('요구사항에 따라 반경 r만 입력받는 사용자 정의 함수는 kms_24013976_func_3.m 으로 저장되었습니다.');
global V; 
V = 10; % 체적 10 in^3 할당
disp('-> 함수 내부와 외부에서 체적값을 공유하기 위해 V를 전역 변수(global)로 선언했습니다.');
disp(' ');

% --------------------------------------------------
% [ (c)번 풀이: 최적화, 도달 높이, 민감도 조사 ]
% --------------------------------------------------
disp('[(c) 최적화 계산 및 민감도 분석]');

% fminbnd 함수를 사용하여 면적 A를 최소화하는 r값 계산
[r_opt, A_min] = fminbnd(@kms_24013976_func_3, 0.1, 10);

% 상응하는 높이 h 계산 (h = 3V / (pi*r^2))
h_opt = (3 * V) / (pi * r_opt^2); 

fprintf('1. 표면적을 최소화하는 최적 반경 r = %.4f in\n', r_opt);
fprintf('2. 상응하는 원뿔의 높이 h = %.4f in\n', h_opt);
fprintf('3. 이때의 최소 표면적 A = %.4f in^2\n\n', A_min);

% 민감도 조사 (면적이 최소값 위로 10% 증가하는 경계)
A_limit = A_min * 1.10; 

% 방정식 A(r) - A_limit = 0 의 근(경계값) 도출
limit_eq = @(r) kms_24013976_func_3(r) - A_limit;
r_lower = fzero(limit_eq, r_opt - 0.5); % 최적값 기준 왼쪽 한계점
r_upper = fzero(limit_eq, r_opt + 1.0); % 최적값 기준 오른쪽 한계점

fprintf('<해의 민감도 조사>\n');
fprintf('최소 표면적에서 10%% 증가한 한계 면적값: %.4f in^2\n', A_limit);
fprintf('허용되는 반경(r)의 오차 범위: %.4f in ~ %.4f in\n', r_lower, r_upper);
fprintf('결론: 반경 r은 최적값(%.4f)에서 마이너스 방향으로 %.4f in, 플러스 방향으로 %.4f in 만큼 벗어나도 표면적 증가는 10%% 이내입니다.\n', r_opt, r_opt - r_lower, r_upper - r_opt);

% R에 대한 A의 그래프를 그려서 시각화
r_val = linspace(0.5, 4, 500);
A_val = arrayfun(@kms_24013976_func_3, r_val);

figure; 
plot(r_val, A_val, 'b-', 'LineWidth', 1.5); hold on;
plot(r_opt, A_min, 'ro', 'MarkerSize', 8, 'MarkerFaceColor', 'r');
yline(A_limit, 'g--', 'LineWidth', 1.5); 
plot([r_lower, r_upper], [A_limit, A_limit], 'ks', 'MarkerSize', 8, 'MarkerFaceColor', 'k');
grid on; 
title('문제 3: 원뿔 반경 R에 따른 표면적 A 최적화 및 민감도'); 
xlabel('반경 R (in)'); 
ylabel('표면적 A (in^2)');
legend('표면적 A(r)', '최소 표면적 (최적 반경)', '10% 면적 증가 허용선', '오차 허용 경계점', 'Location', 'best'); 
hold off;



%% 4. 익명 함수 생성 및 그래프 작성
disp('=== 문제 4 ===');

% 문제 요구사항: 함수 f(x) = 10e^(-2x) 에 대한 익명 함수 생성
f4 = @(x) 10 * exp(-2 * x); 

% 요구사항: 구간 0 <= x <= 2 설정
x4_val = linspace(0, 2, 500);

% 함수의 그래프 작성
figure; 
plot(x4_val, f4(x4_val), 'm-', 'LineWidth', 2);
grid on; 

% 안내사항 준수: 축 이름, 제목, 범례를 적절히 포함할 것
title('문제 4: f(x) = 10e^{-2x} 의 그래프'); 
xlabel('x'); 
ylabel('f(x)');
legend('f(x) = 10e^{-2x}', 'Location', 'best');



%% 5. 익명 함수 생성 및 2차 함수의 최소값
% 주어진 수식: 30x^2 - 300x + 4
f5 = @(x) 30*x.^2 - 300*x + 4;

% (a) 최소값의 대략적인 위치를 결정하기 위한 그래프 작성
% 대칭축이 x = 5 이므로 0부터 10까지 범위를 설정하여 포물선을 그립니다.
x5_val = linspace(0, 10, 500);

figure; 
plot(x5_val, f5(x5_val), 'c-', 'LineWidth', 2); hold on;
grid on; 
title('문제 5: f(x) = 30x^2 - 300x + 4'); 
xlabel('x'); 
ylabel('f(x)');

% (b) fminbnd 함수를 이용하여 최소값의 위치를 정확하게 결정
[x5_min, f5_min] = fminbnd(f5, 0, 10);

% 정확한 최소값 위치를 그래프에 빨간 점으로 표시
plot(x5_min, f5_min, 'ro', 'MarkerSize', 8, 'MarkerFaceColor', 'r');
legend('f(x)', '정확한 최소값', 'Location', 'best'); 
hold off;

% 결과 출력
fprintf('\n[문제 5]\n');
fprintf('(a) 그래프를 통해 대략 x=5 위치에서 최소값을 가짐을 시각적으로 확인했습니다.\n');
fprintf('(b) fminbnd로 구한 정확한 최소값 위치: x = %.4f\n', x5_min);
fprintf('    이때의 함수값: f(x) = %.4f\n', f5_min);




%% 6. 4개의 익명 함수 생성 및 합성 함수 그래프 작성
disp('=== 문제 6 ===');

% 1. 문제에서 제시된 3개의 기본 익명 함수 생성
f_6 = @(x) x.^2;             % f(x) = x^2
g_6 = @(y) 3*cos(y);         % g(y) = 3*cos(y)
h_6 = @(z) 6*exp(z);         % h(z) = 6*e^z

% 2. 위 함수들을 사용하여 구성된 4번째 최종 합성 익명 함수 생성
% h(g(f(x))) 형태로 합성하면 6*exp(3*cos(x^2))이 됩니다.
composite_func = @(x) h_6(g_6(f_6(x))); 

% 3. 지정된 범위 0 <= x <= 4 에서 그래프 작성
x6_val = linspace(0, 4, 1000);
y6_val = composite_func(x6_val);

figure;
plot(x6_val, y6_val, 'k-', 'LineWidth', 1.5);
grid on;

% 안내사항 준수: 축 이름, 제목, 범례 포함
title('문제 6: 4개의 익명 함수로 구성된 6e^{3cos(x^2)} 그래프');
xlabel('x');
ylabel('f(x)');
legend('6e^{3cos(x^2)}', 'Location', 'best');

% 결과 확인 출력
fprintf('생성된 익명 함수 개수: 4개 (f_6, g_6, h_6, composite_func)\n');
disp('-> 범위 0 <= x <= 4 에서의 그래프 작성을 완료했습니다.');
