# deadlock
/*
 * detector.c
 *
 *  Created on: 2018 M11 7
 *      Author: liux8275
 */

# include <stdio.h>
# include <stdlib.h>
# include <string.h>

int main(void){
	//setbuf(stdout, NULL);

	char str[11];
	char* token;
	int num_p;
	int num_r;
	int l = 0;
	int flag;
	int k;
	int count_gone=0;
	int n=0;
	int is_deadlock = 0;
	int is_deadlock1= 0;

	fgets(str, 5, stdin);
	token = strtok(str, " ");
	num_p = atoi(token);
	token = strtok(NULL, " ");
	num_r = atoi(token);

	int requested[num_p][num_r];
	int allocated_arr[num_p][num_r];
	int unallocate_arr[num_r];

	int work[num_r];//
	int complete_p[num_p];//
	int finish[num_p];//
	int delete_p[num_p-1];

	int total_run = 2*num_p + 1;
	int len_fgets = num_r * 2 + 1;
	fgets(str, len_fgets, stdin);

	for (int i=0;i<total_run;i++){
		token = strtok(str, " ");
		if (i < num_p){
			requested[i][0] = atoi(token);
		}
		if (i>=num_p && i<(total_run-1)){
			allocated_arr[i-num_p][0] = atoi(token);
		}
		if (i == (total_run-1)){
			unallocate_arr[0] = atoi(token);
		}
		for(int j=1;j<num_r;j++){
			token = strtok(NULL, " ");
			if (i < num_p){
				requested[i][j] = atoi(token);
			}
			if (i>=num_p && i<(total_run-1)){
				allocated_arr[i-num_p][j] = atoi(token);
			}
			if (i == (total_run - 1)){
				unallocate_arr[j] = atoi(token);
			}
		}
		fgets(str, len_fgets, stdin);

	}

	for (int i=0;i<num_p;i++){
		finish[i] = 0;
	}
	for (int i=0;i<num_p;i++){
		work[i] = unallocate_arr[i];
	}

	while (l<num_p){
		flag = 0;
		for (int j=0;j<num_r;j++){
			if (finish[j]  == 0){
				for (k=0;k<num_r;k++){
					if (requested[j][k]>work[k]){
						break;
					}
				}
				if (k == num_r){

					for (int o=0;o<num_p;o++){
						work[o] = allocated_arr[j][o] + work[o];
					}
					complete_p[count_gone]=j+1;
					count_gone ++;
					finish[j] = 1;

					l++;
					flag = 1;
				}
			}
		}
		if (flag == 0){
			is_deadlock = 1;
			is_deadlock1 = 1;
			if (count_gone>0){
				for (int j=0;j<num_p;j++){
					for (int z=0;z<count_gone;z++){
						if (j+1 != complete_p[z])
						printf("%d ",j+1);
					}
				}
			}
			else{
				for (int j=0;j<num_p;j++){
					printf("%d ",j+1);
				}
			}
			break;
		}
	}
	if (is_deadlock == 1){
		printf("\n");
	}

	int x;
	int y=0;
	while (is_deadlock != 0){
		for (int j=0;j<num_r;j++){
			work[j] = allocated_arr[n][j] + work[j];
		}
		delete_p[n] = n+1;
		finish[n] = 1;
		while (y<num_p-n-1){
			flag = 0;
			for (int t=n+1;t<num_p;t++){
				if (finish[t]  == 0){
					for (x=0;x<num_r;x++){
						if (requested[t][x]>work[x]){
							break;
						}
					}
					if (x == num_r){
						for (int o=0;o<num_p;o++){
							work[o] = allocated_arr[t][o] + work[o];
						}
						complete_p[count_gone]=t+1;
						count_gone ++;
						finish[t] = 1;
						y++;
						flag = 1;
					}
				}
			}
			if (flag == 0){
				break;
			}
			else{
				is_deadlock = 0;
			}
		}

		n++;
	}
	if (is_deadlock1 == 0){
		for (int j=0;j<count_gone;j++){
			printf("%d ",complete_p[j]);
		}
		printf("\n");
	}
	else{
		for (int j=0;j<n;j++){
			printf("%d ",delete_p[j]);
		}
		printf("\n");
		for (int j=0;j<count_gone;j++){
			printf("%d ",complete_p[j]);
		}
	}

	return(0);
}
