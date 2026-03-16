# SmartGov

> Smart Office System for Government Excellence
>  
> Comprehensive smart office solution designed for government agencies to enhance efficiency, transparency, and service delivery through digital transformation. 
>
## Product Functions
- #### Citizen Engagement
  1. Customer Satisfaction Measurement
  2. Transaction Management
  3. Public Information Display

- #### Internal Processes
  1. Human Resource Management
  2. Face Recognition Attendance Recording 
  3. Employee Onboarding
  4. Employee Morale Survey
  5. Activity Scheduler
  6. Incoming Communications Management
  7. Recruitment, Selection and Placement (RSP)
  8. Learning and Development (L&D)
  9. Performance Management (PM)
  10. Rewards and Recognition (R&R)

- #### Other Features
  1. Analytics Dashboards
  2. Report Generation
  3. Secure, Compliant, Customizable, and AI-powered 


## System Requirements
### Web App
 - [ ] [LAMP/LEMP Stack](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu)
	 - Linux (>= 22.04 LTS) - operating system
	 - Apache/Nginx - web server software.
	 - MySQL/MariaDB - open-source relational database management systems
	 - PHP (>= 8.1)- server-side scripting language
 - [ ] [PHP composer](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-22-04)
 - [ ] Git
### Face Recognition API
[Checkout Face Recognition API here](https://github.com/DOST6/face-recognition.git)
 - [ ] Inception-Resnet V1 Models in PyTorch
 - [ ] MongoDB
 - [ ] Python FastAPI
 - [ ] Nginx
## Pre-requisites
 1. Setup LAMP/LEMP server stack
 2. Install required PHP extensions for CodeIgniter 4.5 or later 
	 - [intl](https://www.php.net/manual/en/intl.requirements.php)
	 - [mbstring](https://www.php.net/manual/en/mbstring.requirements.php)
	 - [json](https://www.php.net/manual/en/json.requirements.php)
     - Refer to CodeIgniter documentation (https://codeigniter.com/user_guide/intro/requirements.html)
 3. Ensure `git` is installed
 4. Ensure `composer` is installed
 5. Setup empty database and dedicated user

## Installation

1. Clone repo: `git clone git@github.com:DOST6/smartgov.git`
2. Run `composer install`
3. Copy `env_sample` to `.env` and modify as needed
4. (Optional) Configure server config (Apache `virtual host` or nginx `server blocks`) as needed
5. (Optional) Configure HTTPS using Let's Encrypt
	- https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-22-04
	- https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu

## Run database migrations
1. `php spark migrate`
2. `php spark migrate -n 'Modules\EMS'`
3. `php spark migrate -n 'Modules\Onboarding'`
4. `php spark migrate -n 'Modules\RSP'`
5. `php spark migrate -n 'Modules\RewardsRecognition'`
6. `php spark migrate -n 'Modules\LearningDevelopment'`
7. `php spark migrate -n 'Modules\PerformanceManagement'`
8. `php spark migrate -n 'Modules\Scheduler'`
9. `php spark migrate -n 'Modules\CSM'`
10. `php spark migrate -n 'Modules\Transactions'`
11. `php spark migrate -n 'Modules\IncomingCommunications'`

## Run database seeders
1. `php spark db:seed AppSeeder`
2. `php spark db:seed 'Modules\EMS\Database\Seeds\EMSQuestionsSeeder'`
3. `php spark db:seed 'Modules\LearningDevelopment\Database\Seeds\LDSeeder'`
4. `php spark db:seed 'Modules\PerformanceManagement\Database\Seeds\PMSeeder'`
5. `php spark db:seed 'Modules\RewardsRecognition\Database\Seeds\RewardsAndRecognitionSeeder'`
6. `php spark db:seed 'Modules\Onboarding\Database\Seeds\OnboardingSeeder'`
7. `php spark db:seed 'Modules\CSM\Database\Seeds\CSMQuestionsSeeder'`
8. `php spark db:seed 'Modules\IncomingCommunications\Database\Seeds\IncomingCommunicationsSeeder'`

## Operationalize the basic system - HR Records Management and DTR module

 - [ ] Open the system through the IP address or domain name, if defined
 - [ ] Login using default admin account: `smarthr.ph@gmail.com`/`adm1n@SmartHR`
 - [ ] Setup required system libraries
   - [ ] Organization Structure
   - [ ] Positions
   - [ ] Position Items
   - [ ] Request Process Flow
   - [ ] Geofences
   - [ ] Attendance Scheme
   - [ ] Holidays
   - [ ] Leave Types
   - [ ] Office Logo
  
 - [ ] Manage Employees
   - [ ] Employees
   - [ ] Employments/Appointments
   - [ ] User Accounts
  
## Operationalize all other features of the system

 - [ ] Using the default admin account, setup other system libraries
   - [ ] Courses
   - [ ] Schools 
   - [ ] Exam Types
   - [ ] AVP URL
   - [ ] Salary Schedules
   - [ ] Services

 - [ ] Employee Onboarding
 - [ ] Employee Morale Survey
 - [ ] Activity Scheduler
 - [ ] Incoming Communications Management
 - [ ] Recruitment, Selection and Placement
 - [ ] Learning and Development
 - [ ] Performance Management
 - [ ] Rewards and Recognition
  


## Setup Cron Job
 - [ ] Schedule sessions cleanup at 00:03 every day (adjust as necessary)
`3 0 * * * /usr/bin/php /var/www/smartgov.ph/smartgov/spark sessions:cleanup >> /var/www/smartgov.ph/smartgov/writable/logs/ci_sessions_cleanup.log 2>&1`
 - [ ] Schedule faces cleanup hourly (adjust as necessary)
`5 * * * * /usr/bin/php /var/www/smartgov.ph/smartgov/spark faces:cleanup >> /var/www/smartgov.ph/smartgov/writable/logs/ci_faces_cleanup.log 2>&1`

  
