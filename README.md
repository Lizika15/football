# football
Приложение для получения данных футбольного турнирам
def ask(table):
    print ('Если вы хотите увидеть все матчи по данной дате - введите 0, если по определенной команде - 1, если рейтинговую таблицу - 2:')
    k = int(input())
    if k == 0:
        print('Введите дату в формате ДД/ММ/ГГГГ')
        t = str(input())
        print()
        dk=[]
        for i in range(len(table['Date'])):
            if table['Date'][i] == t:
                dk.append(i)
        if dk == []:
            print('В этот день соревнований не было')
        else:
            print_by_date(table, dk)

    if k == 1:
        print('Введите название команды:')
        t = str(input())
        print()
        dk=[]
        for i in range(len(table['HomeTeam'])):
            if (table['HomeTeam'][i] == t) or (table['AwayTeam'][i] == t) :
                dk.append(i)
        if dk == []:
            print('В этот день соревнований не было')
        else:
            print_by_date(table, dk)

    if k == 2:
        teams = create_teams(table)
        add_score(table, teams)
        namet = sort_teams(teams)
        teams_end={}
        for i in namet:
            teams_end[i] = teams[i]
        print_teams(teams_end)
        


def create_table(fd, index):
    name = ['Date', 'HomeTeam', 'AwayTeam', 'HG', 'AG']
    fd.readline()
    for i in fd:
        line = i.split(',')
        for k in range(len(index)):
            table[name[k]].append(line[index[k]])
    return table


def create_teams(table):
    teams = {}
    k = 1
    for j in range(len(table['Date'])):
        if table['HomeTeam'][j] not in teams:
            teams[table['HomeTeam'][j]] = {'N': k, 'PLAYS': 0, 'WIN': 0, 'DRAW': 0, 'LOST': 0, 'GA': 0, 'GF': 0, 'GD': 0, 'SC': 0 } #GA-забито, GF-пропущено, GD -разница
            k += 1
        
        if table['AwayTeam'][j] not in teams:
            teams[table['AwayTeam'][j]] = {'N': k, 'PLAYS': 0, 'WIN': 0, 'DRAW': 0, 'LOST': 0, 'GA': 0, 'GF': 0, 'GD': 0, 'SC': 0 } #GA-забито, GF-пропущено, GD -разница
            k += 1
    return teams



def add_score(table, teams):
    for i in range(len(table['Date'])):
        team1 = table['HomeTeam'][i]
        teams[team1]['PLAYS'] += 1
        teams[team1]['GA'] += int(table['HG'][i])
        teams[team1]['GF'] += int(table['AG'][i])
        p = (int(table['HG'][i]) - int(table['AG'][i]))
        teams[team1]['GD'] += p
        if p < 0:
            teams[team1]['LOST'] += 1
        if p == 0:
            teams[team1]['DRAW'] += 1
            teams[team1]['SC'] += 1
        if p > 0:
            teams[team1]['WIN'] += 1
            teams[team1]['SC'] += 3

        team2 = table['AwayTeam'][i]
        teams[team2]['PLAYS'] +=1
        teams[team2]['GA'] += int(table['AG'][i])
        teams[team2]['GF'] += int(table['HG'][i])
        p = (int(table['AG'][i]) - int(table['HG'][i]))
        teams[team2]['GD'] += p
        if p < 0:
            teams[team2]['LOST'] += 1
        if p == 0:
            teams[team2]['DRAW'] += 1
            teams[team2]['SC'] += 1
        if p > 0:
            teams[team2]['WIN'] += 1
            teams[team2]['SC'] += 3
    return teams


def sort_teams(teams):
    namet = []
    for i in teams:
        namet.append(i)
    i = 0
    for i in range(len(teams)):
        j = 1
        for j in range(len(teams)):
            if teams[namet[j]]['SC'] < teams[namet[i]]['SC']:
                teams[namet[i]]['N'], teams[namet[j]]['N'] = teams[namet[j]]['N'], teams[namet[i]]['N'] 
                namet[i], namet[j]=namet[j], namet[i]
            if teams[namet[j]]['SC'] == teams[namet[i]]['SC']:
                if teams[namet[j]]['WIN'] < teams[namet[i]]['WIN']:
                    teams[namet[i]]['N'], teams[namet[j]]['N'] = teams[namet[j]]['N'], teams[namet[i]]['N'] 
                    namet[i], namet[j]=namet[j], namet[i]
                if teams[namet[j]]['WIN'] == teams[namet[i]]['WIN']:
                    if teams[namet[j]]['GD'] < teams[namet[i]]['GD']:
                        teams[namet[i]]['N'], teams[namet[j]]['N'] = teams[namet[j]]['N'], teams[namet[i]]['N'] 
                        namet[i], namet[j]=namet[j], namet[i]
                    if teams[namet[j]]['GD'] == teams[namet[i]]['GD']:
                        if teams[namet[j]]['GA'] < teams[namet[i]]['GA']:
                            teams[namet[i]]['N'], teams[namet[j]]['N'] = teams[namet[j]]['N'], teams[namet[i]]['N'] 
                            namet[i], namet[j]=namet[j], namet[i]
    return namet


def print_by_date(table, d):
    first = ['Date', 'HomeTeam', 'AwayTeam', 'Home Goals', 'Away Goals']
    for i in range(len(first)):
        if i == (len(first) - 1):
            print("\033[36m|{0:^25}|".format(first[i]))
        else:
            print("\033[36m|{0:^25}".format(first[i]), end='')
    print('|=================================================================================================================================|')
    for i in d:
        second=[table['Date'][i], table['HomeTeam'][i], table['AwayTeam'][i], table['HG'][i], table['AG'][i]]
        k = 0
        for k in range(len(second)):
            if k == (len(first) - 1):
                print("\033[0m|{0:^25}|".format(second[k]))
            else:
                print("\033[0m|{0:^25}".format(second[k]), end='')


def print_teams(teamk):
    first = ['N', 'TEAM', 'PLAYS', 'W', 'D', 'L', 'GA', 'GF', 'GD', 'SC']
    for i in range(len(first)):
        if i == (len(first) - 1):
            print("\033[36m|{0:^10}|".format(first[i]))
        elif i == 1:
            print("\033[36m{0:<25}".format(first[i]), end='')
        else:
            print("\033[36m|{0:^10}".format(first[i]), end='')
    print('|===========================================================================================================================|')
    key = list(teamk.keys())
    key_ind = 0
    for k in teamk:
        second = [teamk[k]['N'], key[key_ind], teamk[k]['PLAYS'], teamk[k]['WIN'], teamk[k]['DRAW'], teamk[k]['LOST'], teamk[k]['GA'], teamk[k]['GF'], teamk[k]['GD'], teamk[k]['SC']]
        for ki in range(len(second)):
            if ki == (len(second) - 1):
                print("\033[0m|{0:^10}|".format(second[ki]))
            elif ki == 1:
                print("\033[0m{0:<25}".format(second[ki]), end='')
            else:
                print("\033[0m|{0:^10}".format(second[ki]), end='')
        key_ind +=1


table = {'Date':[], 'HomeTeam': [], 'AwayTeam': [], 'HG': [], 'AG': []}
while True:
    print('Если вы хотите выбрать данные по "Russian Premier League 2018-2019" - введите 0, если по "England Premiership 2018-2019" - введите 1:')
    a = int(input())
    if a == 0:
        fd = open("RUS20182019.csv", "r")
        index = [3, 5, 6, 7, 8]
        table = create_table(fd, index)
        ask(table)
    if a == 1:
        fd = open("E0.csv", "r")
        index = [1, 2, 3, 4, 5]
        table = create_table(fd, index)
        ask(table)
    print()
    print('Вы хотите продолжить? Да/Нет')
    y = input()
    print()
    if y == 'Да': continue
    if y == 'Нет': break

fd.close()
