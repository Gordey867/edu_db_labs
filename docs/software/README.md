# Реалізація інформаційного та програмного забезпечення

## SQL-скрипт для створення та початкового наповнення бази даних

```sql
-- Форвард-інженерія MySQL Workbench
SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='TRADITIONAL,ALLOW_INVALID_DATES';

-- -----------------------------------------------------
-- Схема ProjectManagementSystem
-- -----------------------------------------------------
DROP SCHEMA IF EXISTS `ProjectManagementSystem`;
CREATE SCHEMA IF NOT EXISTS `ProjectManagementSystem` DEFAULT CHARACTER SET utf8;
USE `ProjectManagementSystem`;

-- -----------------------------------------------------
-- Таблиця `User`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `User`;
CREATE TABLE IF NOT EXISTS `User` (
  `id`           INT NOT NULL AUTO_INCREMENT,
  `nickname`     VARCHAR(255) NULL,
  `email`        VARCHAR(255) NULL,
  `password`     VARCHAR(255) NULL,
  `avatar`       VARCHAR(255) NULL,
  `blockStatus`  TINYINT      NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC)
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Permission`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Permission`;
CREATE TABLE IF NOT EXISTS `Permission` (
  `id` INT NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC)
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Role`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Role`;
CREATE TABLE IF NOT EXISTS `Role` (
  `id`   INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(255) NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC)
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Grant`  (зв’язок Role ↔ Permission, M:N)
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Grant`;
CREATE TABLE IF NOT EXISTS `Grant` (
  `Permission_id` INT NOT NULL,
  `Role_id`       INT NOT NULL,
  PRIMARY KEY (`Permission_id`, `Role_id`),
  INDEX `fk_Grant_Permission_idx` (`Permission_id` ASC),
  INDEX `fk_Grant_Role_idx`       (`Role_id`       ASC),
  CONSTRAINT `fk_Grant_Permission`
    FOREIGN KEY (`Permission_id`)
    REFERENCES `Permission`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `fk_Grant_Role`
    FOREIGN KEY (`Role_id`)
    REFERENCES `Role`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Project`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Project`;
CREATE TABLE IF NOT EXISTS `Project` (
  `id`          INT NOT NULL AUTO_INCREMENT,
  `name`        VARCHAR(255) NULL,
  `description` VARCHAR(255) NULL,
  `manager`     VARCHAR(255) NULL,
  `isArchived`  TINYINT      NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC)
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Team`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Team`;
CREATE TABLE IF NOT EXISTS `Team` (
  `id`         INT NOT NULL AUTO_INCREMENT,
  `name`       VARCHAR(255) NULL,
  `Project_id` INT NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  INDEX `fk_Team_Project_idx` (`Project_id` ASC),
  CONSTRAINT `fk_Team_Project`
    FOREIGN KEY (`Project_id`)
    REFERENCES `Project`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Task`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Task`;
CREATE TABLE IF NOT EXISTS `Task` (
  `id`          INT NOT NULL AUTO_INCREMENT,
  `name`        VARCHAR(255) NULL,
  `description` VARCHAR(255) NULL,
  `deadline`    DATETIME      NULL,
  `priority`    VARCHAR(45)   NULL,
  `difficulty`  VARCHAR(45)   NULL,
  `Project_id`  INT           NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  INDEX `fk_Task_Project_idx` (`Project_id` ASC),
  CONSTRAINT `fk_Task_Project`
    FOREIGN KEY (`Project_id`)
    REFERENCES `Project`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Collaborator`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Collaborator`;
CREATE TABLE IF NOT EXISTS `Collaborator` (
  `id`       INT NOT NULL AUTO_INCREMENT,
  `User_id`  INT NOT NULL,
  `Role_id`  INT NOT NULL,
  `Team_id`  INT NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  INDEX `fk_Collab_User_idx` (`User_id` ASC),
  INDEX `fk_Collab_Role_idx` (`Role_id` ASC),
  INDEX `fk_Collab_Team_idx` (`Team_id` ASC),
  CONSTRAINT `fk_Collab_User`
    FOREIGN KEY (`User_id`)
    REFERENCES `User`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `fk_Collab_Role`
    FOREIGN KEY (`Role_id`)
    REFERENCES `Role`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `fk_Collab_Team`
    FOREIGN KEY (`Team_id`)
    REFERENCES `Team`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB;

-- -----------------------------------------------------
-- Таблиця `Assignment`
-- -----------------------------------------------------
DROP TABLE IF EXISTS `Assignment`;
CREATE TABLE IF NOT EXISTS `Assignment` (
  `id`              INT NOT NULL AUTO_INCREMENT,
  `datetime`        DATETIME    NULL,
  `Task_id`         INT         NOT NULL,
  `Collaborator_id` INT         NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  INDEX `fk_Assign_Task_idx`        (`Task_id` ASC),
  INDEX `fk_Assign_Collaborator_idx`(`Collaborator_id` ASC),
  CONSTRAINT `fk_Assign_Task`
    FOREIGN KEY (`Task_id`)
    REFERENCES `Task`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `fk_Assign_Collab`
    FOREIGN KEY (`Collaborator_id`)
    REFERENCES `Collaborator`(`id`)
    ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB;

-- Відновлює перевірки, якщо це потрібно
SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;

-- Вставка даних у таблицю `User`
INSERT INTO `User` (`nickname`, `email`, `password`, `avatar`, `blockStatus`) 
VALUES 
('john_doe', 'john@example.com', 'password123', 'avatar.jpg', 0),
('jane_smith', 'jane@example.com', 'securepass', 'avatar2.jpg', 1);

-- Вставка даних у таблицю `Permission`
INSERT INTO `Permission` ()
VALUES 
(NULL), 
(NULL);

-- Вставка даних у таблицю `Role`
INSERT INTO `Role` (`name`)
VALUES 
('Admin'), 
('User');

-- Вставка даних у таблицю `Grant` (M:N зв’язок між `Role` і `Permission`)
INSERT INTO `Grant` (`Permission_id`, `Role_id`)
VALUES 
(1, 1),  -- Role 'Admin' має Permission '1'
(2, 2);  -- Role 'User' має Permission '2'

-- Вставка даних у таблицю `Project`
INSERT INTO `Project` (`name`, `description`, `manager`, `isArchived`) 
VALUES 
('Project A', 'Description of Project A', 'Alice', 0),
('Project B', 'Description of Project B', 'Bob', 0);

-- Вставка даних у таблицю `Team`
INSERT INTO `Team` (`name`, `Project_id`) 
VALUES 
('Team 1', 1),
('Team 2', 2);

-- Вставка даних у таблицю `Task`
INSERT INTO `Task` (`name`, `description`, `deadline`, `priority`, `difficulty`, `Project_id`) 
VALUES 
('Task 1', 'Description for Task 1', '2025-06-30 12:00:00', 'High', 'Medium', 1),
('Task 2', 'Description for Task 2', '2025-07-15 12:00:00', 'Low', 'Easy', 2);

-- Вставка даних у таблицю `Collaborator`
INSERT INTO `Collaborator` (`User_id`, `Role_id`, `Team_id`) 
VALUES 
(1, 1, 1),  -- John is Admin in Team 1
(2, 2, 2);  -- Jane is User in Team 2

-- Вставка даних у таблицю `Assignment`
INSERT INTO `Assignment` (`datetime`, `Task_id`, `Collaborator_id`) 
VALUES 
('2025-06-01 10:00:00', 1, 1),  -- John is assigned to Task 1
('2025-06-02 14:00:00', 2, 2);  -- Jane is assigned to Task 2

```

